# Tomcat Tutorial: Konfiguration von RemoteAddrValve und AccessLogValve

In diesem Tutorial lernst du, wie du die RemoteAddrValve und die AccessLogValve in Apache Tomcat konfigurierst, um den Zugriff auf deine Webanwendungen zu steuern und Zugriffe zu protokollieren.

> Dokumentation: https://tomcat.apache.org/tomcat-9.0-doc/config/valve.html

## Voraussetzungen

1. Stelle sicher das die folgenden Schritte als User `tomcat` durchgeführt werden: `sudo -u tomcat /bin/bash`

## Schritt 1: Konfiguration der RemoteAddrValve

Die `RemoteAddrValve` dient dazu, den Zugriff auf eine Tomcat-Webanwendung basierend auf der IP-Adresse des Clients zu steuern. Das ist nützlich, um den Zugang auf bestimmte Netzwerke zu beschränken.

### 1.1 Öffnen der server.xml

Die Konfiguration erfolgt in der `server.xml`, die sich im `conf`-Verzeichnis deiner Tomcat-Installation befindet. Öffne diese Datei mit einem Texteditor:

```bash
sudo -u tomcat vi /opt/tomcat/current/conf/server.xml
```

### 1.2 Hinzufügen der RemoteAddrValve

Füge die folgende Konfiguration in den `<Host>`-Abschnitt deiner `server.xml`-Datei ein:

```xml
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
       deny=".*"
       allow="192\.168\.1\.100" />

```

**Erklärung:**

- `allow`: Liste der erlaubten IP-Adressen oder IP-Bereiche, hie eine einzelne IP-Adresse.
- `deny`: Alle anderen Adressen werden blockiert.

### 1.3 Neustart von Tomcat

Nachdem du die Änderungen gespeichert hast, musst du Tomcat neu starten, damit die Änderungen wirksam werden:

```bash
sudo systemctl restart tomcat
```

### 1.4 Überprüfe das Ergebnis

* Überprüfe über den Webbrowser ob der Tomcat erreichbar ist: http://localhost:8080/bella. Es sollte eine Meldung kommen:

```text
Access to localhost was denied
You don't have authorisation to view this page.
HTTP ERROR 403
```

* Überprüfe das Access Log File. Der Code 403 bedeutet "HTTP Forbidden".

```text
127.0.0.1 - - [xx/xxx/xxxx:xx:xx:xx +0000] "GET /bella HTTP/1.1" 403 -
```

### 1.4 Anpassung der Konfiguration
* Entferne wieder die Konfiguraton und Starte den Tomcat neu
* Überprüfe über den Webbrowser ob der Tomcat erreichbar ist: http://localhost:8080/bella


## Schritt 2: Konfiguration der AccessLogValve

Die `AccessLogValve` wird verwendet, um alle Zugriffe auf deine Tomcat-Webanwendungen zu protokollieren. Dies ist besonders hilfreich für das Monitoring und die Fehleranalyse.

### 2.1 Hinzufügen der AccessLogValve

- Suche die Konfiguration AccessLogValve in dr `server.xml` für `localhost`
- Ergänze das Logformat um die Protokollierung der lokalen Port-Nummer und ergänze im Pattern `Port:%p`:

```xml
<Valve className="org.apache.catalina.valves.AccessLogValve"         
        directory="logs"
        prefix="localhost_access_log" suffix=".txt"
        pattern="%h %l %u %t &quot;%r&quot; %s %b Port:%p" />
```


> Dokumentation Access Log Patterns: https://tomcat.apache.org/tomcat-7.0-doc/api/org/apache/catalina/valves/AccessLogValve.html

**Erklärung:**

- `directory`: Das Verzeichnis, in dem die Logs gespeichert werden. Standardmäßig ist dies das `logs`-Verzeichnis in der Tomcat-Installation.
- `prefix` und `suffix`: Der Dateiname des Logfiles. In diesem Fall wird es `localhost_access_log.txt` heißen.
- `pattern`: Das Format, in dem die Log-Einträge protokolliert werden. `%h` steht für die IP-Adresse des Clients, `%r` für die angeforderte URL, `%s` für den HTTP-Statuscode und so weiter. Dieses Muster kann bei Bedarf angepasst werden.
- `fileDateFormat`: Der Zeitstempel im Dateinamen, der das Logfile für jeden Tag trennt.


### 2.2 Neustart von Tomcat

Wie auch bei der `RemoteAddrValve` musst du nach den Änderungen Tomcat neu starten:

```bash
sudo systemctl restart tomcat
```

## Schritt 3: Überprüfen der Logs und Zugriffe

Nach dem Neustart überprüfe das Access Log. Die Port-Nummer sollte jetzt am Ende erscheinen:

```text
127.0.0.1 - - [xx/xxx/xxxx:xx:xx:xx +0000] "GET /bella HTTP/1.1" 403 - Port:8080
```


## Fazit

In diesem Tutorial hast du gelernt, wie du die `RemoteAddrValve` konfigurierst, um den Zugriff auf bestimmte IP-Adressen zu beschränken, und wie du mit der `AccessLogValve` die Zugriffe auf deine Webanwendungen protokollierst. Dies sind nützliche Werkzeuge für die Sicherheit und das Monitoring deiner Tomcat-Server.

