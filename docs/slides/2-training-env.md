# Trainings-Umgebung
<br>
<br>

--

## Tools

Die Trainings-Umgebung ist vorinstalliert mit folgenden Tools:<br>

- Windows 10
- OpenJDK 11
- VisualStudio Code
- Internet Information Server (IIS)
<br>
<br>

VirtualBox mit einem Ubuntu 24 Gast-System:<br>
- OpenJDK 11
- Apache 2
- OpenSSH - User: <span style="color: cornflowerblue">training/training</span>

--

## Ports

| Windows - IIS Webserver    | http://localhost:80   |
|----------------------------|-----------------------|
| Windows - Tomcat           | http://localhost:8080 |
| Ubuntu - Apache Webserver  | http://localhost:8000 |
| Ubuntu - Tomcat            | http://localhost:8888 |
| SSH                        | localhost:2222        |


--

## Downloads
* [Download OpenJdk](https://learn.microsoft.com/de-de/java/openjdk/download#openjdk-11)
* [Download Tomcat 9](https://tomcat.apache.org/download-90.cgi)
* [Download IIS JK-Connector](https://dlcdn.apache.org/tomcat/tomcat-connectors/jk/binaries/windows/)
* [Download VS Code](https://code.visualstudio.com/download)
* [Download Visual C++ Redistributable](https://support.microsoft.com/de-de/topic/aktuelle-unterst%C3%BCtzte-downloads-f%C3%BCr-visual-c-2647da03-1eea-4433-9aff-95f26a218cc0)
* [Download VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Download Ubuntu](https://ubuntu.com/download/server)

--

## Windows Desktop
Installation Skript:
* Windows 10 (10 GB, 8 CPU, 50GB HDD)
* OpenJDK 11
* VisualStudio Code als Editor
* Internet Information Server (IIS) (Control Panel > Programs > Turn Windows Features On or Off)
* Visual C++ 2019 Redistributable ist installiert für VirtualBox
* VirtualBox ist installiert
* File Extensions im Explorer sind aktiviert

--

## Ubuntu Server
In VirtualBox ist ein Ubuntu Gast System installiert:
- User: training/training
- Guest Additions sind installiert
- OpenJDK, Apache 2 und OpenSSH sind installiert:
```shell

# OpenJDK 11 als Voraussetzung für Tomcat
sudo apt update
sudo apt-get install openjdk-11-jdk

# Apache 2 Webserver zum testen von mod_jk / mod_ajp
sudo apt install apache2
sudo systemctl status apache2


# OpenSSH ermöglicht den Zugriff per SSH vom Host auf den Gast
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh

```
