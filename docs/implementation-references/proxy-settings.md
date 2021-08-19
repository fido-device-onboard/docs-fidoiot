
# Proxy Settings for Running FDO 
This document lists the different proxy settings that needs to be set 
## In .bashrc
Add this line 
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
After successful PRI build, add the proxy settings to the Dockerfile file in the path <fdo-pri-src>/component-samples/demo/<rv/owner/manufacturer>

```
ENV http_proxy <proxy host>:<port>
ENV https_proxy <proxy host>:<port>

```

## Proxy Settings for Docker* 
Follow the proxy settings detailed [here](#https://secure-device-onboard.github.io/docs-fidoiot/latest/installation/) 
