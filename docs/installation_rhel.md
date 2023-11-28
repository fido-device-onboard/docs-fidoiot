

# Installation Guide (RHEL)
This document can be used as a quick start guide to setup the development environment. Please review the system requirements listed below before moving forward with the FDO installation and deployment.

## System Requirements

| Component | Recommended |
|------- |------|
| Host Operating System | RHEL* (8.4, 8.6) |
| Podman* Engine | v3.4.2+|
| Podman* Compose | v1.0.3 |
| Maven* | 3.8.3 |
| Java | 11 |
| Haveged | - |

## Podman* Installation
Podman is included in the `container-tools` module. To install podman run the following commands:
```
sudo yum module enable -y container-tools:rhel8
sudo yum module install -y container-tools:rhel8
```

***NOTE:*** If you are working behind a proxy, ensure to set proper proxy variables in `/etc/environment` and `/etc/rhsm/rhsm.conf`



## Podman* Compose Installation
To install a specific version of Podman\* Compose (for example **_1.0.3_**) follow these steps:

1 . Download the specific version **(1.0.3)** of Podman* Compose.
```
sudo curl -o /usr/local/bin/podman-compose https://raw.githubusercontent.com/containers/podman-compose/v1.0.3/podman_compose.py
```
2 . To apply executable permissions, run the following command.
```
sudo chmod +x /usr/local/bin/podman-compose
sudo ln -s /usr/local/bin/podman-compose /usr/bin/podman-compose
```

## Other Development Tools

### 1 . To install OpenJDK*
```
sudo yum -y install java-17-openjdk-devel
```

### 2 . To install Maven*

1. Start by downloading the Apache Maven archive in the /tmp directory with wget command:
```
wget https://archive.apache.org/dist/maven/maven-3/3.8.3/binaries/apache-maven-3.8.3-bin.tar.gz -P /tmp
```
2. Once the download is complete, extract the archive in the /opt directory:
```
sudo tar xf /tmp/apache-maven-3.8.3-bin.tar.gz -C /opt
```
3. Create a symbolic link maven that will point to the Maven installation directory:
```
sudo ln -s /opt/apache-maven-3.8.3 /opt/maven
```
4. Set up the environment variables by creating a new file named maven.sh in the /etc/profile.d/ directory and paste the following code:
```
export JAVA_HOME=/usr/lib/jvm/jre-openjdk
export M2_HOME=/opt/maven
export MAVEN_HOME=/opt/maven
export PATH=${M2_HOME}/bin:${PATH}
```
5. Make the script executable by running the following chmod command:
```
sudo chmod +x /etc/profile.d/maven.sh
```
6. Load the environment variables using the source command:
```
source /etc/profile.d/maven.sh
```
7. Verify the installation
To verify that Maven is installed, use the `mvn -version` command which will print the Maven version:
```
mvn -version
```
You should see something like the following:
```
Apache Maven 3.8.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: /opt/maven
Java version: 11.0.5, vendor: Oracle Corporation, runtime: /usr/lib/jvm/java-11-openjdk-11.0.5.10-0.el8_0.x86_64
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "4.18.0-80.7.1.el8_0.x86_64", arch: "amd64", family: "unix"
```

### 3 . To install Haveged
```
sudo yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum -y install haveged
sudo chkconfig haveged on
```

## To Set Correct System Time

Using NTP (Network Time Protocol) 

First, ensure that the NTP client is installed on your system. If it's not already installed, you can install it using the following command:

```
sudo apt-get update
sudo apt-get install ntp
```

Open the NTP configuration file for editing. This file typically is located at /etc/ntp.conf. Use your preferred text editor, such as nano or vim:

```
sudo nano /etc/ntp.conf
```

Replace or add NTP servers based on your location or preference. For example:

```
server ntp.ubuntu.com
server pool.ntp.org
```

Restart the NTP service to apply the configuration changes

```
sudo systemctl restart ntp
```

Monitor the NTP synchronization status using the following command:

```
ntpq -p
```

## References

[Podman-installation-methods](https://podman.io/getting-started/installation)

[Podman-compose-installation](https://github.com/containers/podman-compose/blob/devel/README.md)

[Maven-installation](https://linuxize.com/post/how-to-install-apache-maven-on-centos-8/)

_*_ represents proprietary software products. FDO claims no rights over the mentioned software products. Use them at your own discretion.
