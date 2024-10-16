# Multiple Tomcat Instanzen auf einem Server
Zielbild:
- /opt/tomcat/current ist unser Verzeichnis für die Installation und beinhalt auch die Konfiguration für den 1. Tomcat
- /opt/tomcat/tomcat2 ist das neue Verzeichnis für die Konfiguration der 2. Tomcat-Instanz. Sie nutzt die Installation von /opt/tomcat/current, hat aber eine eigene Konfiguration.

| Instanz | CATALINA_BASE       | CATALINA_HOME          | Ports      |
|---------|---------------------|------------------------|------------|
| Tomcat 1| /opt/tomcat/current | /opt/tomcat/current    | 8080, 9005 |
| Tomcat 2| /opt/tomcat/tomcat2 | /opt/tomcat/current    | 9080, 9005 |

## Schritt 1: Installation
1. Stelle sicher das die folgenden Schritte als User `tomcat` durchgeführt werden: `sudo -u tomcat /bin/bash`
2. Erstelle das Verzeichnisse `/opt/tomcat/tomcat2`
3. Kopiere die Dateien von /opt/tomcat/current/* nach /opt/tomcat/tomcat2/
4. Lösche in /opt/tomcat/tomcat2/bin alle Dateien ausser `setenv.sh`
5. Konfiguriere die Ports auf die neuen Werte (je nachdem wie der Stand deiner Installation ist direkt in der `server.xml` oder über Umgebungsvariablen in der `setenv.sh`)
6. Erstelle eine zweite Service Konfiguration

```bash

sudo cp /etc/systemd/system/tomcat.service /etc/systemd/system/tomcat2.service

```

7. Ändere die Datei und ergänze CATALINA_HOME:
   Inhalt der Datei:
```plaintext
Description=Apache Tomcat 2
After=network.target

[Service]
Type=forking
ExecStart=/opt/tomcat/current/bin/startup.sh
ExecStop=/opt/tomcat/current/bin/shutdown.sh
User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

# CATALINA_BASE
Environment="CATALINA_BASE=/opt/tomcat/tomcat2"

[Install]
WantedBy=multi-user.target
```

8. Lade den Dienst neu
```bash
sudo systemctl daemon-reload
```

9. Starten den Tomcat 2:
```bash
sudo systemctl start tomcat2
```

10. Überprüfen den Status des Tomcat 2:
```bash
sudo tail -f /opt/tomcat/tomcat2/logs/catalina.out
```

11. Überprüfe den Tomcat 2 im Webbrowser auf dem neuen Port 9080
Falls erforderlich stelle ein weitere Port-Forwarding her: `ssh -L 9080:localhost:9080 -p 2222 training@localhost`
