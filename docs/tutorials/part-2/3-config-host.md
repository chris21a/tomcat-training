# Tutorial: Konfiguration von Hosts

## Voraussetzungen
1. Stelle sicher das die folgenden Schritte als User `tomcat` durchgeführt werden: `sudo -u tomcat /bin/bash`

## Schritt 1: Einen zweiten Host konfigurieren
1. Erstelle einen zweiten Host in der Engine Konfiguration
2. Erstelle einen Hostname Alias für 127.0.0.1 mit dem Namen "www.acme.com"
3. Öffne die Datei `server.xml` und ergänze folgende Konfiguration:

```xml

      <Host name="www.acme.com"  appBase="acme" unpackWARs="true" autoDeploy="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="www_acme_com_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
      </Host>
  
```

4. Kopiere das WAR-File [bella-versiontest.war](..%2Ffiles%2Fbella-versiontest.war) nach `/opt/tomcat/current/acme`
4. Starte den Tomcat neu und überprüfe das Ergebnis: http://www.acme.com:8080/bella
5. Eventuell musst du das Port-Forwarding überprüfen
6. Überprüfe auch ob das WAR-File unter `acme` entpackt wurde
7. Überprüfe auch ob das neue Access Lofile  `logs/www_acme_com_access_log`vorhanden ist.

## Schritt 2: Einen Host Alias konfigurieren

1. Erstelle für die "deutsche Niederlassung" einen Hostname Alias für 127.0.0.1 it dem Namen `www.acme.de`
2. Öffne die Datei `server.xml` und ergänze folgende Konfiguration:

```xml

      <Host name="www.acme.com" appBase="acme" unpackWARs="true" autoDeploy="true">
        ... 
               
        <Alias>www.acme.de</Alias>
        
        ...       
      </Host>
  
```
3. Starte den Tomcat neu und überprüfe das Ergebnis unter http://www.acme.de:8080/bella
