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

Stelle sicher das diese Konfiguration in der workers.properties vorhanden ist:

```bash
sudo vi /etc/libapache2-mod-jk/workers.properties
```

````markdown
# Define the worker list
worker.list=ajp13_worker

# Define the worker configuration for Tomcat
worker.tomcat.type=ajp13
worker.tomcat.host=localhost
worker.tomcat.port=8009

````



### Schritt 4: Konfiguriere jk.conf

```bash
sudo vi etc/apache2/mods-available/jk.conf
```

Ensure the following configuration:
```markdown
JkWorkersFile /etc/libapache2-mod-jk/workers.properties
JkLogFile /var/log/apache2/mod_jk.log
JkLogLevel info
JkShmFile /var/log/apache2/jk-runtime-status

# The bella-Webapp
JkMount /bella/* ajp13_worker

```

### Schritt 5: mod_jk aktivieren

```shell

sudo a2enmod jk

```


### Schritt 6: Apache restarten

```shell

### Schritt 5: mod_jk aktivieren

```shell

sudo systemctl restart apache2

```

```