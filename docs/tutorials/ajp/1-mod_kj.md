# Tutorial: mod_jk
### Schritt 1: Vorbereitung

Bevor du beginnst, stelle sicher, dass du die folgenden Software-Komponenten installiert hast:

- **Apache HTTP Server** (httpd)
- **Apache Tomcat** (mit aktivem AJP Connector)
- **Bella App**: Stelle sicher das die Webapp bella.war unter http://localhost:8080 deployed ist.


### Schritt 2: Installiere das Mod_JK-Modul

Zunächst musst du das **mod_jk**-Modul für Apache installieren. Je nach Betriebssystem können die Befehle variieren.

```bash
sudo apt-get install libapache2-mod-jk
```

### Schritt 3: AJP-Connector in Tomcat konfigurieren

Der AJP-Connector ermöglicht die Kommunikation zwischen Apache und Tomcat über das AJP-Protokoll. Der Connector muss in der **`server.xml`** von Tomcat aktiviert oder konfiguriert werden.

> Der Konnektor sollte bereits in deiner Installation konfiguriert sein.

1. Öffne die **`server.xml`** in **`$CATALINA_BASE/conf/server.xml`**.
2. Finde den Abschnitt für den AJP-Connector und stelle sicher, dass er aktiviert ist:

```xml
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
```

3. Starte Tomcat neu:

```bash
sudo systemctl restart tomcat
```

### Schritt 4: Mod_JK in Apache konfigurieren

#### 1. Mod_JK Modul laden

Füge in der Apache-Konfigurationsdatei **`httpd.conf`** oder einer separaten Konfigurationsdatei (z. B. **`/etc/httpd/conf.d/mod_jk.conf`**) die Konfiguration für **mod_jk** hinzu.

Öffne die Datei **`httpd.conf`** oder erstelle **`mod_jk.conf`** und füge Folgendes hinzu:

```apache
# Laden des mod_jk Moduls
LoadModule jk_module modules/mod_jk.so

# Logfile für Mod_JK
JkLogFile /var/log/httpd/mod_jk.log
JkLogLevel info

# JKWorkersFile: Verweis auf die Datei, die die Tomcat-Worker definiert
JkWorkersFile /etc/httpd/conf/workers.properties

# JKShmFile: Shared Memory für mod_jk
JkShmFile /var/log/httpd/mod_jk.shm
```

#### 2. Tomcat-Worker in workers.properties definieren

Erstelle die Datei **`workers.properties`** (falls sie noch nicht existiert) unter **`/etc/httpd/conf/workers.properties`**.

Füge die folgende Konfiguration für Tomcat-Worker hinzu:

```properties
# Liste der Worker
worker.list=tomcat1

# Definition des Tomcat-Workers
worker.tomcat1.type=ajp13
worker.tomcat1.host=localhost
worker.tomcat1.port=8009
```

- **`worker.list=tomcat1`**: Definiert den Namen des Workers.
- **`worker.tomcat1.host=localhost`**: Adresse des Tomcat-Servers (localhost bei demselben Server).
- **`worker.tomcat1.port=8009`**: AJP-Port des Tomcat-Servers.

#### 3. Apache konfigurieren, um Anfragen an Tomcat weiterzuleiten

Füge in **`httpd.conf`** oder **`mod_jk.conf`** die Zuordnung von Apache-URLs zu Tomcat hinzu:

```apache
# Mounten der URL /bella auf Tomcat
JkMount /bella/* tomcat1
```

Damit wird jede Anfrage, die mit **`/bella`** beginnt, an den Tomcat-Worker `tomcat1` weitergeleitet.

### Schritt 5: Restart und Überprüfung

1. Starte Apache neu, um die Änderungen zu übernehmen:

```bash
sudo systemctl restart httpd
```

2. Überprüfe, ob Tomcat über Apache zugänglich ist. Öffne den Browser und gehe zu:

```
http://localhost/bella
```

Wenn alles richtig konfiguriert ist, sollte die Tomcat-Webanwendung unter **`/bella`** angezeigt werden.

### Schritt 6: Mod_JK Logging und Troubleshooting

- **Logfiles**: Überprüfe die Mod_JK-Logs für Fehler oder Statusinformationen.
    - **Apache Log**: `/var/log/httpd/error_log`
    - **Mod_JK Log**: `/var/log/httpd/mod_jk.log`

- **Debugging**: Erhöhe den Log-Level in der **`mod_jk.conf`**:
  ```apache
  JkLogLevel debug
  ```

- **Firewall**: Stelle sicher, dass der AJP-Port (standardmäßig 8009) in der Firewall geöffnet ist, falls Tomcat und Apache auf verschiedenen Servern laufen.

### Schritt 7: Optional – Load Balancing einrichten

Falls du mehrere Tomcat-Instanzen hinter Apache nutzen möchtest, kannst du Load-Balancing einrichten. Dazu definierst du mehrere Tomcat-Worker und einen Load-Balancer-Worker.

**workers.properties**:

```properties
# Liste der Worker
worker.list=loadbalancer

# Tomcat Instanz 1
worker.tomcat1.type=ajp13
worker.tomcat1.host=localhost
worker.tomcat1.port=8009

# Tomcat Instanz 2
worker.tomcat2.type=ajp13
worker.tomcat2.host=localhost
worker.tomcat2.port=8010

# Load-Balancer
worker.loadbalancer.type=lb
worker.loadbalancer.balance_workers=tomcat1,tomcat2
```

**httpd.conf**:

```apache
# Alle Anfragen an /bella auf den Load-Balancer weiterleiten
JkMount /bella/* loadbalancer
```

### Zusammenfassung:

- Installiere **mod_jk** und konfiguriere es, um Anfragen an Tomcat weiterzuleiten.
- Konfiguriere den AJP-Connector in Tomcat und stelle sicher, dass Tomcat die Anfragen über den richtigen Port empfängt.
- Nutze **workers.properties**, um die Tomcat-Worker zu definieren.
- Überprüfe die Logs und stelle sicher, dass die Anbindung erfolgreich ist.

Mit dieser Anleitung kannst du erfolgreich eine Verbindung zwischen **Apache Web Server** und **Tomcat** mit **mod_jk** herstellen.