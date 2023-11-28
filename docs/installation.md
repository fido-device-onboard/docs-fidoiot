# Installation Guide

This document can be used as a quick start guide to setup the development environment. Please review the system requirements listed below before moving forward with the FDO installation and deployment.

## System Requirements

| Component             | Recommended                                          |
| --------------------- | ---------------------------------------------------- |
| Host Operating System | Ubuntu\* (20.04, 22.04) / Debian 11.4 / Windows\* 10 |
| Docker\* Engine       | 20.10.10+                                            |
| Docker\* Compose      | 1.29.2                                               |
| Maven\*               | 3.5.4                                                |
| Java                  | 17                                                   |
| Haveged               | -                                                    |

## Docker\* Installation

[Follow the steps](https://docs.docker.com/engine/install/ubuntu/) to setup Docker\* environment.

### Running the Docker\* Behind a Proxy

To run the Docker\* system behind a proxy server, the configuration is done by the following steps:

1 . Directory `docker.service.d` is to be created in systemd directory as shown below.

```
mkdir -p /etc/systemd/system/docker.service.d
```

2 . For HTTP proxy, create a file **_http-proxy.conf_** in the above created directory and add the following content to this file.

```
[Service]
Environment="HTTP_PROXY=<Proxy IP/URL:Port>"
```

3 . For HTTPS proxy, create a file **_https-proxy.conf_** in the above created directory and add the following content to this file.

```
[Service]
Environment="HTTPS_PROXY=<Proxy IP/URL:Port>"
```

4 . Next, create a directory named **_.docker_** in the user home path (**~/**) and create a file named **_config.json_** if not present, add the following content.

```
{
    "proxies":
    {
        "default":
        {
            "httpProxy": "<Proxy IP/URL:Port>",
            "httpsProxy": "<Proxy IP/URL:Port>"
            "noProxy": "<Proxy IP/URL:Port>"
        }
    }
}
```

5 . After configuring the above, the Docker\* service needs to be restarted.

```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

6 . To ensure that the proxies are set successfully, run the following command

```
sudo systemctl show --property Environment docker
```

7 . FDO Docker\* FAQs

1. Docker* Time Synchronization Issue while building Ubuntu* 20 docker image from an Ubuntu\* 18 machine. [Refer](https://github.com/fido-device-onboard/all-in-one-demo/issues/62)

2. Failure in device onboarding due to the inaccessibility of internet (while running Docker\* behind a proxy network). [Refer](https://github.com/fido-device-onboard/all-in-one-demo/issues/63)

## Docker\* Compose Installation

To install a specific version of Docker\* Compose (for example **_1.29.2_**) follow these steps:

1 . Download the specific version **(1.29.2)** of Docker\* Compose.

```
sudo curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-`uname -s`-`uname -m` -o /usr/bin/docker-compose
```

2 . To apply executable permissions, run the following command.

```
sudo chmod +x /usr/bin/docker-compose
```

3 . To ensure that the required version is installed, run ` docker-compose --version` command.

## Other Development Tools

1 . To install OpenJDK\*

```
sudo apt install openjdk-17-jdk-headless
```

2 . To install Maven\*

```
sudo apt install maven
```

3 . To install Haveged

```
sudo apt install haveged
```

## To Set Correct System Time Zone

First it is important to setup the correct Timezone.

1. Using tzdata command (Method 1)

Open CLI and run the command

```
sudo dpkg-reconfigure tzdata
```

Follow the onscreen prompts to set the timezone correctly.

2. Using timedatectl command (Method 2)

To get a list of timezones, run the following command

```
timedatectl list-timezones
```

Select the appropriate timezone and run the following command

```
sudo timedatectl set-timezone <Timezone>
```

Now check if the timezone is set properly by running the following command

```
timedatectl
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

[Docker-installation-methods](https://docs.docker.com/engine/install/ubuntu/#installation-methods)

[Docker-compose-installation](https://docs.docker.com/compose/install/)

[Setting-proxy-for-docker](https://docs.docker.com/network/proxy/)

_\*_ represents proprietary software products. FDO claims no rights over the mentioned software products. Use them at your own discretion.
