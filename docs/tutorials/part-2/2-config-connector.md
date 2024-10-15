# Tutorial: Konfiguration von Connectoren

## Voraussetzungen
1. Stelle sicher das die folgenden Schritte als User `tomcat` durchgeführt werden: `sudo -u tomcat /bin/bash`

## Einen zweiten HTTP-Connetor konfigurieren
1. Erstelle einen zweiten HTTP/1.1 Konnektor auf Port 8765 und aktivem X-Powered-By Header und einem eigenen Executor.
2. Öffne die Datei `server.xml` und ergänze folgende Konfiguration:

```xml
    <Executor name="smallThreadPool" namePrefix="small-pool-"
        maxThreads="4" minSpareThreads="2"/>
        
        <Connector executor="smallThreadPool"
               port="8765" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               xpoweredBy="true">    
```

3. Nach dem Neustart überprüfe das Ergebnis:
- Ist der neue Thread-Pool im Tomcat Status zu sehen?
- Öffne den Browser auf Port 8765. Überprüfe mit den Developer Tools des Browsers ob der Repsonse Header X-Powered-By gesetzt ist. Welchen Wert hat der Header? 
4. Alternativ benutze `curl -I http://localhost:8765` (nur Header anzeigen)


## Einen AJP-Connetor konfigurieren
1. Erstelle einen AJP-Connector auf Port 8009
2. Öffne die Datei `server.xml` und ergänze folgende Konfiguration:

```xml
   <Connector protocol="AJP/1.3"
   address="::1"
   port="8009"
   redirectPort="8443"
   maxParameterCount="1000"
   />
```
3. Nach dem Neustart überprüfe das Ergebnis:
- Was passiert wenn man den Port im Webbrowser öffnet? http://localhost:8009
4. Alternativ benutze `curl -v http://localhost:8009` (verbose Output)
5. Überprüfe ob der Connector auh im Tomcat Status angezeigt wird.


