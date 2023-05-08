# Proxy Settings for Running FDO

This document lists the different proxy settings that need to be set. 

## Script to Automate Proxy Setup

```shell
#!/bin/bash
#
# USAGE:
# sudo ./proxy_setup.sh http_proxy https_proxy ftp_proxy socks_proxy no_proxy
#
# SAMPLE USGAE:
#
# sudo ./proxy_setup.sh http://<dns>:911/ https://<dns>:912/
#
# NOTE:
#
# Parameters are optional. Supply parameters only if you want to override the default proxy values.
# By default the IP range for no proxy includes subnetwork 244 and 247, please add the subnetwork range accordingly.
#
# First Param: http_proxy
# Second Param: https_proxy
# Third Param: ftp_proxy
# Fourth Param: socks_proxy
#

ip_range="@@(echo 127.0.{244..247}.{1..254},)" #change it according to your ip range.
sys_ip_addr=$(ip route get 8.8.8.8 | grep -oP 'src \K[^ ]+')

default_http_proxy="http://<dns>:911/"
default_https_proxy="http://<dns>:912/"
default_ftp_proxy="http://<dns>:911/"
default_socks_proxy="http://<dns>:1080/"
default_no_proxy="localhost,127.0.0.1,$ip_range"

http_proxy=${1:-$default_http_proxy}
https_proxy=${2:-$default_https_proxy}
ftp_proxy=${3:-$default_ftp_proxy}
socks_proxy=${4:-$default_socks_proxy}
no_proxy=${5:-$default_no_proxy}

http_proxy_host=$(echo $http_proxy | awk -F':' {'print $2'} | tr -d '/')
http_proxy_port=$(echo $http_proxy | awk -F':' {'print $3'} | tr -d '/')
https_proxy_host=$(echo $https_proxy | awk -F':' {'print $2'} | tr -d '/')
https_proxy_port=$(echo $https_proxy | awk -F':' {'print $3'} | tr -d '/')

_JAVA_OPTIONS="-Dhttp.proxyHost=$http_proxy_host -Dhttp.proxyPort=$http_proxy_port -Dhttps.proxyHost=$https_proxy_host -Dhttps.proxyPort=$https_proxy_port -Dhttp.nonProxyHosts=localhost|127.0.0.1|*.mycompany.com|$sys_ip_addr"

# OS-Level Proxy Settings

sed -i '/_proxy/d' /etc/environment
sed -i '/_PROXY/d' /etc/environment

echo -n "http_proxy=\"$http_proxy\"" >> /etc/environment
echo >> /etc/environment
echo -n "https_proxy=\"$https_proxy\"" >> /etc/environment
echo >> /etc/environment
echo -n "ftp_proxy=\"$ftp_proxy\"" >> /etc/environment
echo >> /etc/environment
echo -n "socks_proxy=\"$socks_proxy\"" >> /etc/environment
echo >> /etc/environment

# No Proxy and JAVA Proxy settings for bash shell applicable to all users

echo "export no_proxy=\"$no_proxy\"" >> /etc/bash.bashrc
sed -i 's/@@(echo /$(echo /'  /etc/bash.bashrc
echo -n "export _JAVA_OPTIONS=\"$_JAVA_OPTIONS\"" >> /etc/bash.bashrc

# Docker Proxy Settings

if [ -x "$(command -v docker)" ]; then
   docker --version
   mkdir -p /etc/systemd/system/docker.service.d
   cd /etc/systemd/system/docker.service.d

   if [ -f http-proxy.conf ] ; then
     mv http-proxy.conf http-proxy_org.conf
     echo "http-proxy.conf is backed up at location /etc/systemd/system/docker.service.d"
   fi

   echo "[Service]" >> http-proxy.conf
   echo "Environment=\"HTTP_PROXY=$http_proxy\"" >> http-proxy.conf

   if [ -f https-proxy.conf ] ; then
     mv https-proxy.conf https-proxy_org.conf
     echo "https-proxy.conf is backed up at location /etc/systemd/system/docker.service.d"
   fi

   echo "[Service]" >> https-proxy.conf
   echo "Environment=\"HTTPS_PROXY=$https_proxy\"" >> https-proxy.conf

   uname=`logname`
   mkdir -p /home/$uname/.docker
   cd /home/$uname/.docker/

   if [ -f config.json ] ; then
     mv config.json config_org.json
     echo "config.json is backed up at location ~/.docker"
   fi

   echo "{" >> config.json
   echo "    \"proxies\":" >> config.json
   echo "    {" >> config.json
   echo "        \"default\":" >> config.json
   echo "        {" >> config.json
   echo "            \"httpProxy\": \"$http_proxy\"," >> config.json
   echo "            \"httpsProxy\": \"$https_proxy\"," >> config.json
   echo "            \"noProxy\": \"*.mycompany.com,127.0.0.1,localhost,$sys_ip_addr\"" >> config.json
   echo "        }" >> config.json
   echo "    }" >> config.json
   echo "}" >> config.json
  
   systemctl daemon-reload
   systemctl restart docker

   systemctl show --property Environment docker --no-pager
else
   echo "Docker is not installed."
fi

echo "Restart the session for proxies to take effect."
```

## In .bashrc

Add this line: 

```
export _JAVA_OPTIONS="-Dhttp.proxyHost=<proxy host> -Dhttp.proxyPort=<port> -Dhttps.proxyHost=<proxy host> -Dhttps.proxyPort=<port>"
```

## Maven settings.xml 

Create a settings.xml file in ~/.m2 folder (if it does not already exist) and add the below content. Replace with the actual proxy host and port details. 

```
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.
http://maven.apache.org/xsd/settings-1.0.0.xsd">

<proxies>
<proxy>
<id>http_proxy</id>
<active>true</active>
<protocol>http</protocol>
<host><proxy host></host>
<port> <port> </port>
<username></username>
<password></password>
<nonProxyHosts>localhost|<proxy details></nonProxyHosts>
</proxy>
<proxy>
<id>https_proxy</id>
<active>true</active>
<protocol>https</protocol>
<host><proxy host></host>
<port><port></port>
<username></username>
<password></password>
<nonProxyHosts>localhost|<proxy></nonProxyHosts>
</proxy>
</proxies>
</settings>
```

## In Dockerfile 

After successful PRI build, add the proxy settings to the Dockerfile file in the path <fdo-pri-src>/component-samples/demo/<rv/owner/manufacturer>.

```
ENV http_proxy <proxy host>:<port>
ENV https_proxy <proxy host>:<port>

```

## Proxy Settings for Docker* 

Follow the proxy settings detailed [here](https://fido-device-onboard.github.io/docs-fidoiot/latest/installation/). 
