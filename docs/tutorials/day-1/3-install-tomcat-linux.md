# Tomcat und Linux installieren

# Schritt 1: User anlegen
1. Loggen sich auf dem Ubuntu Gast-System ein mit dem User `training`.
2. Erstellen Sie einen neuen Benutzer `tomcat` mit folgendem Befehl:
```bash
sudo useradd -r -m -U -d /opt/tomcat -s /bin/false tomcat
```

# Schritt 2: Tomcat herunterladen und entpacken

```bash
  curl -O https://downloads.apache.org/tomcat/tomcat-9/v9.0.96/bin/apache-tomcat-9.0.96.tar.gz
  
  sudo tar xf apache-tomcat-9.0.96.tar.gz  -C /opt/tomcat/

  sudo ln -s /opt/tomcat/apache-tomcat-9.0.96 /opt/tomcat/current

  sudo chown -R tomcat: /opt/tomcat
  sudo sh -c 'chmod +x /opt/tomcat/current/bin/*.sh'
 ```

# Schritt 3: Environment konfigurieren
1. setenv.sh erstellen 

```bash
sudo vi /opt/tomcat/current/bin/setenv.sh
```

Inhalt der Datei:
```bash
#!/bin/sh
JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
CATALINA_PID=/opt/tomcat/current/temp/tomcat.pid
CATALINA_HOME=/opt/tomcat/current
CATALINA_BASE=/opt/tomcat/current
CATALINA_OPTS="-Xms512M -Xmx1024M -server -XX:+UseParallelGC"
JAVA_OPTS=-Djava.awt.headless=true
```

2. Berechtigungen setzen

```bash
chmod u+x /opt/tomcat/current/bin/setenv.sh
```

# Schritt 4: Tomcat im Vordergrund starten 
1. Um die Installation zu überprüfen Tomcat im Vordergrund starten
```bash
sudo -u tomcat /opt/tomcat/current/bin/catlina.sh run
```

2. Zum Stopen Ctrl+C drücken

# Schritt 5: Service konfigurieren
Linux verwendet `systemd` um Services zu verwalten.

1. Erstellen Sie eine neue Service-Datei für Tomcat:
```bash
sudo vi /etc/systemd/system/tomcat.service
```

Inhalt der Datei:
```plaintext
Description=Apache Tomcat
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

[Install]
WantedBy=multi-user.target
```

2. Laden Sie die Service-Datei neu damit die Konfiguration übernommen wird:
```bash
sudo systemctl daemon-reload
```

3. Starten Sie den Tomcat Service:
```bash
sudo systemctl start tomcat
```

4. Überprüfen Sie den Status des Tomcat Service:
```bash
sudo tail -f /opt/tomcat/current/logs/catalina.out
```

5. Stoppen Sie den Tomcat Service:
```bash
sudo systemctl stop tomcat
```
