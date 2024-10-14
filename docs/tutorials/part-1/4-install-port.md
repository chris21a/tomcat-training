# Installieren von Multi-Instanz-Tomcats

## Schritt 1: Port über Setenv setzen
1. Erstelle in setenv.sh Umgebungsvariablen für die Ports 8080 für HTTP und 8105 für SHUTDOWN
2. Ergänge die JAVA_OPTS Variable um die Umgebungsvariable zu setzen:



## Schritt 1: Installation 
1. Erstelle Verzeichnisse für eine zweites CATALINA_BASE.
2. Kopie die Dateien von /opt/tomcat/current nach /opt/tomcat/tomcat2/
3. Löschen in /opt/tomcat/tomcat2/bin alle Dateien ausser setenv.sh
4. Ändere in setenv.sh die Umgebungsvariablen für den 2. Tomcat:
    - Konfiguriere die Ports für den 2. Tomcat auf Port 8280 für HTTP und 9105 für SHUTDOWN
5. Starte den 2. Tomcat und überprüfe das Ergebnis:

```bash
CATALINA_HOME=/opt/tomcat/tomcat2 /opt/tomcat/current/bin/startuo.sh
```


```xml
<Server port="9005" shutdown="SHUTDOWN">
  <Service name="Catalina">

    <Connector port="9080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               maxParameterCount="1000"
               />

```

4. Der `redirectPort` kann hier ignoriert werden dan kein SSL Connector definiert wurde.
5. Starten sie den Service neu und überprüfen das Ergebnis.

# Verwenden von Umgebungvariablen
Das Ändern von Ports direkt im `server.xml` ist anfällig für ungewollte Änderung. Eine Konfiguration über Umgebungs-Variablen ist eine Alternative in neueren Tomcat

1. Setzen sie für den 2. Tomcat die Option im `setenv.sh` Skript

```bash
CATALINA_OPTS="$CATALINA_OPTS -Dorg.apache.tomcat.util.digester.PROPERTY_SOURCE=org.apache.tomcat.util.digester
EnvironmentPropertySource"

 SHUTDOWN_PORT=9105
 HTTP_PORT=9180

```

2. Nutzen sie in der server.xml die Variablen Ersetzung:


```xml
<Server port="${SHUTDOWN_PORT}" shutdown="SHUTDOWN">
  <Service name="Catalina">

    <Connector port="${HTTP_PORT}" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               maxParameterCount="1000"
               />

```

3. Starten sie den Tomcat neu und überprüfen das Ergebnis.
