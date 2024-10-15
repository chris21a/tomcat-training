# Ports über Umgebungsvariablen setzen

## Schritt 1: setenv.sh anpassen
1. Editiere die Datei `bin/setenv.sh`:
   - Property EnvironmentPropertySource in `CATALINA_OPTS` setzen 
   - Umgebungsvariablen definieren

```bash
CATALINA_OPTS="$CATALINA_OPTS -Dorg.apache.tomcat.util.digester.PROPERTY_SOURCE=org.apache.tomcat.util.digester.EnvironmentPropertySource"

ENV_PORT_SHUTDOWN=9105
ENV_PORT_HTTP=9181


```

2. Editiere die Datei `conf/server.xml` und ersetze die Port-Nummern durch Platzhalter-Variablen:

```xml
<Server port="${ENV_PORT_SHUTDOWN}" shutdown="SHUTDOWN">
  <Service name="Catalina">
    <Connector port="${ENV_PORT_HTTP}" protocol="HTTP/1.1" ...

```

3. Starten sie den Tomcat neu und überprüfen das Ergebnis.
4. Ändere die Ports über `setenv.sh` wieder auf 8005 und 8080 und starte den Tomcat erneut.
