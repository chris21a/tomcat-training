# Trainings-Umgebung kennenlenen

## Vorinstallierte Tools
Die Trainings-Umgebung ist vorinstalliert mit folgenden Tools:

* Windows 10
* OpenJDK 11
* VisualStudio Code als Editor
* Internet Information Server (IIS) (Control Panel > Programs > Turn Windows Features On or Off)
* Visual C++ 2019 Redistributable ist installiert für VirtualBox
* Oracle VirtualBox ist installiert
* File Extensions im Explorer sind aktiviert

In der VirtualBox ist ein Ubunut 24 Gast-System installiert:
* OpenJDK 11
* OpenSSH
* Apache 2
* Lynx

## Port-Mappings
| Windows - IIS Webserver     | http://localhost:80   |
|-----------------------------|-----------------------|
| Windows - Tomcat            | http://localhost:8080 |
| Ubuntu - Apache Webserver   | http://localhost:8000 |
| Ubuntu - Tomcat             | http://localhost:8888 |
| SSH                         | localhost:2222        |

## Erste Schritte
1. Melden sie sich an der Trainings-Umgebung an mit ihren Zugangsdaten.
2. Starten sie ihren virtuellen Desktop.
3. Starten sie die VirtualBox und starten sie das Gast-System "Ubuntu".
3. Öffnen sie VisualStudio Code
4. Öffnen sie den Webbrowser und navigieren sie zu [http://localhost:80](http://localhost:80). Der IIS-Webserver sollte eine Standardseite anzeigen.
5. Öffnen sie im Webbrowser [http://localhost:8000](http://localhost:8000). Der Apache-Webserver auf dem Ubuntu Gast-System sollte eine Standardseite anzeigen.
4. Öffnen sie ein Windows-Terminal (CMS oder PowerShell)
5. Prüfen sie die Java Version auf dem WIndows System:

```bash
PS C:\Users\student> java -version
openjdk version "11.0.24" 2024-07-16 LTS
OpenJDK Runtime Environment Microsoft-9905872 (build 11.0.24+8-LTS)
OpenJDK 64-Bit Server VM Microsoft-9905872 (build 11.0.24+8-LTS, mixed mode, sharing)
```

6. Starten sie das Gast-System mit dem Namen "Ubuntu"
7. Melden sie sich per SSH am Ubuntu Gast-System. User und Passwort sind "training". Der SSH-Port ist 2222.

```bash
PS C:\Users\student> ssh -p 2222 training@localhost
training@localhost's password: training
Welcome to Ubuntu 24.04.1 LTS (GNU/Linux 6.8.0-45-generic x86_64)
```

8. Prüfen sie die Java Version auf dem Ubuntu Gast-System:
```bash
training@Ubuntu:~$ java -version
openjdk version "11.0.24" 2024-07-16
OpenJDK Runtime Environment (build 11.0.24+8-post-Ubuntu-1ubuntu324.04.1)
OpenJDK 64-Bit Server VM (build 11.0.24+8-post-Ubuntu-1ubuntu324.04.1, mixed mode, sharing)
```

9. Prüfen sie den Status des Apache-Webservers auf dem Ubuntu Gast-System:
```bash
training@Ubuntu:~$ apachectl status
   Apache Server Status for localhost (via 127.0.0.1)

   Server Version: Apache/2.4.58 (Ubuntu)
   Server MPM: event
   Server Built: 2024-07-17T18:55:23
```

# Prima! Sie sind bereit für das Training. Viel Erfolg!
