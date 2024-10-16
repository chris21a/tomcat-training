# Tutorial: mod_jk
### Schritt 1: Vorbereitung

Bevor du beginnst, stelle sicher, dass du die folgenden Software-Komponenten installiert hast:

- **Apache HTTP Server** (httpd)
- **Apache Tomcat** (mit aktivem AJP Connector)
- **Bella App**: Stelle sicher das die Webapp bella.war unter http://localhost:8080 deployed ist.


> Hinweis: Stelle sicher das der Tomcat AJP-Cnnector auf das localhost-Interface gebunden ist.
> Falls ein "address" Attribut im Connector gesetzt ist lösche es. Der Connector muss so konfiguriert sein:

```xml

    <Connector protocol="AJP/1.3"
               port="8009"
               redirectPort="8443"
               maxParameterCount="1000"
               secretRequired="false"
               />

```


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
sudo vi /etc/apache2/mods-available/jk.conf
```

Ensure the following configuration:
```markdown
JkWorkersFile /etc/libapache2-mod-jk/workers.properties
JkLogFile /var/log/apache2/mod_jk.log
JkLogLevel info
JkShmFile /var/log/apache2/jk-runtime-status

```

### Schritt 5: mod_jk aktivieren

```shell

sudo a2enmod jk

```

### Schritt 6: Virtual Host konfigurieren

```bash

vi /etc/apache2/sites-enabled/000-default.conf

```

```xml

<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        JkMount /bella/* ajp13_worker
        JkMount /bella ajp13_worker

</VirtualHost>

```


### Schritt 7: Apache restarten

```shell

### Schritt 5: mod_jk aktivieren

```shell

sudo systemctl restart apache2

```

### Schritt 7: Überprüfe das Ergebnus

Innerhalb des Gastsystems:
```bash

curl http://localhost/bella/

```

Im Browser vom Hostsystem:
```bash

curl http://localhost:8000/bella/

```
