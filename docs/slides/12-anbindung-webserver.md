# Anbindung Webserver

<br><br>
<br><br>
<br><br>

> https://tomcat.apache.org/connectors-doc/index.html


--

## Einleitung

- **Warum Tomcat an Webserver anbinden?**
    - Verbesserung der Performance und Skalierbarkeit
    - Lastverteilung zwischen mehreren Tomcat-Instanzen
    - Nutzung von erweiterten Sicherheitsfunktionen der Webserver

- **Vorteile der Anbindung**
    - Offloading von statischen Inhalten an den Webserver
    - SSL/TLS-Handling durch den Webserver
    - Integration in bestehende Webinfrastrukturen (Apache oder IIS)


--

# AJP und Mod_JK

- **AJP (Apache JServ Protocol)**
    - Binäres Protokoll für die Verbindung zwischen Webserver und Tomcat
    - Schneller und ressourcenschonender als HTTP
    - Ermöglicht die Weiterleitung von Anfragen an Tomcat

- **Mod_JK**
    - Apache-Modul zur Nutzung des AJP-Protokolls
    - Vermittler zwischen Apache und Tomcat
    - Unterstützt Load-Balancing und Failover


--

# Konfiguration von Mod_JK
- **httpd.conf**: Apache-Konfigurationsdatei
- **workers.properties**: Definition der Tomcat-Worker

```text
worker.list=tomcat1

worker.tomcat1.type=ajp13
worker.tomcat1.host=localhost
worker.tomcat1.port=8009
```

- worker.list: Liste der konfigurierten Tomcat-Instanzen
- worker.tomcat1.host: Adresse des Tomcat-Servers
- worker.tomcat1.port: AJP-Port des Tomcat-Servers (standardmäßig 8009)


--

# Konfiguration in httpd.conf


```apache
# Laden des Mod_JK-Moduls
LoadModule jk_module modules/mod_jk.so

# Konfiguration des JKWorkersFile
JkWorkersFile /etc/httpd/conf/workers.properties

# Zuordnung von URLs zu Tomcat
JkMount /app/* tomcat1

# Optional: Weiterleitung aller JSP-Requests an Tomcat
JkMount /*.jsp tomcat1
```

- **JkMount**: Leitet Anfragen für bestimmte Pfade an Tomcat weiter


--

** AJP-Connector in Tomcat

- In Tomcat muss der AJP-Connector konfiguriert sein, um Anfragen vom Apache-Webserver entgegenzunehmen.

### Beispiel: AJP-Connector in server.xml

```xml

<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />

```
- **port**: AJP-Standardport (8009)
- **protocol**: AJP-Protokoll verwenden
- **redirectPort**: Umleitung auf HTTPS, falls erforderlich


--

## Load-Balancing mit Apache und Tomcat

- **Warum Load-Balancing?**
    - Verteilung von Anfragen auf mehrere Tomcat-Instanzen
    - Höhere Verfügbarkeit und Skalierbarkeit

- **Mod_JK Load-Balancing-Konfiguration**

```properties
worker.list=loadbalancer

worker.tomcat1.type=ajp13
worker.tomcat1.host=tomcat1.example.com
worker.tomcat1.port=8009

worker.tomcat2.type=ajp13
worker.tomcat2.host=tomcat2.example.com
worker.tomcat2.port=8009

worker.loadbalancer.type=lb
worker.loadbalancer.balance_workers=tomcat1,tomcat2
```

- **worker.loadbalancer**: Konfiguration des Load-Balancers
- **balance_workers**: Definiert die Tomcat-Instanzen für das Load-Balancing


--

## Troubleshooting und Optimierung

- **Fehlerbehebung**
  - Überprüfen der AJP-Verbindung mit Logs (Tomcat und Apache)
  - Fehlerhafte Pfade in JkMount-Einstellungen prüfen
  - AJP-Ports und Firewalleinstellungen prüfen
  - JK Status Manager: http://localhost/jkstatus

- **Optimierungstipps**
  - Anzahl der maximalen Threads in Tomcat erhöhen (server.xml)
  - Verbindungspooling zwischen Apache und Tomcat aktivieren
  - Nutzung von HTTP/2 und SSL durch Apache mit AJP


--

## Erfahrungsberichte mod_jk und Tomcat

- **Session Sticky Issues**:  
  mod_jk kann in bestimmten Setups die Session-Stickiness verlieren, wodurch Anfragen nicht konsistent an denselben Tomcat-Worker geleitet werden. Dies führt zu verlorenen Sitzungsdaten und fehlerhaften Benutzererfahrungen.

- **Load Balancing Inefficiency**:  
  Eine ungleichmäßige Verteilung der Anfragen tritt auf, wenn mod_jk nicht richtig konfiguriert ist. Dadurch werden einige Tomcat-Instanzen überlastet, während andere nicht ausgelastet sind.

- **Timeout Errors**:  
  Verbindungs-Timeouts und -Abbrüche kommen häufig vor, wenn Parameter wie `socketTimeout` und `retryInterval` nicht optimal eingestellt sind, was zu längeren Antwortzeiten führt.

- **Logging Challenges**:  
  Das Standard-Logging von mod_jk liefert oft nicht genügend Details für die Fehlersuche. Administratoren müssen das Log-Level anpassen, um nützlichere Informationen für die Fehleranalyse zu erhalten.

  
# Microsoft IIS und Tomcat

- IIS fungiert als Webserver, der statische Inhalte bedient.
- Tomcat wird als Anwendungsserver für dynamische Inhalte verwendet.
- **AJP (Apache JServ Protocol)** stellt die Verbindung zwischen IIS und Tomcat her, um HTTP-Anfragen weiterzuleiten.
- **Vorteil**: Lastverteilung und Trennung von statischen und dynamischen Inhalten.
<br><br>
### Voraussetzungen
- Microsoft IIS (Version 8.5 oder höher)
-  ISAPI Redirector für IIS (wird als Connector zwischen IIS und Tomcat verwendet)

--

## Erforderliche Komponenten auf Seite IIS

- **ISAPI Redirector**:
    - Einrichten des Redirectors in IIS durch Hinzufügen von `isapi_redirect.dll`.
    - Diese Datei wird üblicherweise in das Verzeichnis `C:\tomcat\isapi_redirect\` abgelegt.

- **Handler Mappings**:
    - In IIS muss der **Handler Mapping** für die Datei `isapi_redirect.dll` konfiguriert werden.
    - Über den IIS-Manager: **Handler Mappings** → **Add Module Mapping** → Verweis auf `isapi_redirect.dll`.

- **ISAPI Filters**:
    - Hinzufügen von Filtern in IIS, um Anfragen an den ISAPI Redirector zu leiten.
    - Dies geschieht über den IIS-Manager: **ISAPI Filters** → **Add** → Pfad zur `isapi_redirect.dll`.


--

## Konfigurationsbeispiel

### **workers.properties** `C:\tomcat\isapi_redirect\conf\workers.properties`
```properties
worker.list=ajp13
worker.ajp13.port=8009
worker.ajp13.host=localhost
worker.ajp13.type=ajp13
```

### **uriworkermap.properties** `C:\tomcat\isapi_redirect\conf\uriworkermap.properties`
```properties
/myapp/*=ajp13
/*.jsp=ajp13
```

- workers.properties: Definiert, wie der IIS mit dem Tomcat-Server über AJP kommuniziert.
- uriworkermap.properties: Enthält die Zuordnungen der IIS-URLs zu den Tomcat-Anwendungen.


--

# Problemberichte

1. **502-Fehler** treten häufig auf, insbesondere bei hoher Last oder falscher AJP-Konfiguration. Logs liefern oft nicht genug Informationen, um die Ursache genau zu bestimmen【80†source】.

2. **Konfigurationsschwierigkeiten**: In neueren IIS-Versionen wie **IIS 8/10** fehlt oft die ISAPI-Filteroption in der Oberfläche. Diese muss über **Serverrollen** nachinstalliert werden【81†source】.

3. **Stabilitätsprobleme**: Verbindungsabbrüche zwischen IIS und Tomcat sind bekannt. Nutzer empfehlen oft, die **BonCode AJP Connector**-Alternative zu verwenden, da sie stabiler ist【81†source】【79†source】.