
# Installation Guide
This document can be used as a quick start guide to setup the development environment. Please review the system requirements listed below before moving forward with the SDO installation and deployment.

## System Requirements

| Component | Recommended |
|------- |------|
| Operating System | Ubuntu\* 20.04 / Windows\* 10 |
| Docker* Engine | 18.09 |
| Docker* Compose | 1.21.2 |
| Maven* | 3.5.4 |
| Java | 11 |
| Haveged | - |

## Docker* Installation
1 . Removing the older versions of Docker*. If these are installed, uninstall them:
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```
2 . Update the `apt` package index and install packages to allow `apt` to use a repository over HTTPS:
```
 sudo apt-get update
 sudo apt-get install \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg \
      lsb-release
```

!!! NOTE
    If you are working behind a proxy, ensure to set proper proxy variables.

3 . Add official GPG key for Docker*:
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
4 . Use the following command to set up the **stable** repository.
```
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
5 . Update the `apt` package index and install the Docker* Engine 18.09
```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
6 . Verify that Docker* Engine is installed correctly by running the `hello-world` image.
```
sudo docker run hello-world
```

### Running the Docker* Behind a Proxy

To run the Docker* system behind a proxy server, the configuration is done by the following steps:

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

4 . Next, create a directory named **_.docker_** in the user home path (**~/**) and a create a file named **_config.json_** if not present, add the following content.
```
{
    "proxies":
    {
        "default":
        {
            "httpProxy": "<Proxy IP/URL:Port>",
            "httpsProxy": "<Proxy IP/URL:Port>"
        }
    }
}
```

5 . After configuring the above, the Docker* service needs to be restarted.
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

6 . To ensure that the proxies are set successfully, run the following command
```
sudo systemctl show --property Environment docker
```
7 . SDO Docker* FAQs

  1. Docker* Time Synchronization Issue while building Ubuntu* 20 docker image from an Ubuntu* 18 machine. [Refer](https://github.com/secure-device-onboard/all-in-one-demo/issues/62)

  2. Failure in device onboarding due to the inaccessibility of internet (while running Docker* behind a proxy network). [Refer](https://github.com/secure-device-onboard/all-in-one-demo/issues/63)

## Docker* Compose Installation
To install a specific version of Docker\* Compose (for example **_1.21.2_**) follow these steps:

1 . Download the specific version **(1.21.2)** of Docker* Compose.
```
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/bin/docker-compose
```
2 . To apply executable permissions, run the following command.
```
sudo chmod +x /usr/bin/docker-compose
```
3 . To ensure that the required version is installed, run ` docker-compose --version` command

## Other Development Tools

1 . To install OpenJDK*
```
sudo apt install openjdk-11-jdk-headless
```

2 . To install Maven*
```
sudo apt install maven
```

3 . To install Haveged
```
sudo apt install haveged
```

## To set Correct System Time
```
sudo date -s "$(wget -qSO- --max-redirect=0 google.com 2>&1 | grep Date: | cut -d' ' -f5-8)Z"
```

Ensure that the system time is correct, else you will receive the certificate expiration error.

Change Google* domain according to your location.

## References

[Docker-installation-methods](https://docs.docker.com/engine/install/ubuntu/#installation-methods)

[Docker-compose-installation](https://docs.docker.com/compose/install/)

[Setting-proxy-for-docker](https://docs.docker.com/network/proxy/)

_*_ represents proprietary software products. FDO claims no rights over the mentioned software products. Use them at your own discretion.
