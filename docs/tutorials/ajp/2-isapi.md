# Tutorial: Microsoft IIS Anbindung an Apache Tomcat mittels AJP

In dieser Anleitung lernst du, wie du Microsoft IIS als Webserver an Apache Tomcat mittels AJP (Apache JServ Protocol) für die Weiterleitung von HTTP-Anfragen an Tomcat einbindest.

## Voraussetzungen

- Eine installierte **Apache Tomcat** Instanz (Version 9.x oder höher)
- Eine installierte **Microsoft IIS** Instanz (Version 8.5 oder höher)
- **ISAPI Redirector** für IIS (Download von [Tomcat Connectors](https://tomcat.apache.org/download-connectors.cgi))
- Administratorzugang auf dem Server (für die Konfiguration von IIS und Tomcat)
- Die Webapp bella.war ist auf dem Tomcat installiert nter `/bella`

## Schritt 1: Tomcat für AJP vorbereiten

1. Öffne die Datei `server.xml`, die sich im Verzeichnis `conf` deiner Tomcat-Installation befindet.
   ```bash
   C:\tomcat\conf\server.xml
   ```

2. Aktiviere den AJP-Connector in der `server.xml`, indem du folgenden Eintrag hinzufügst oder anpasst:
   ```xml
   <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
   ```

3. Speichere die Datei und starte Tomcat neu, damit die Änderungen übernommen werden.
   ```bash
   sudo systemctl restart tomcat
   ```

## Schritt 2: Installation des ISAPI Redirectors für IIS

1. Lade den **ISAPI Redirector** von der [Tomcat Connectors Website](https://tomcat.apache.org/download-connectors.cgi) herunter.

2. Erstelle ein Verzeichnis für den ISAPI Redirector, z. B. `C:\tomcat\isapi_redirect\`.

3. Kopiere die Datei `isapi_redirect.dll` in dieses Verzeichnis.

4. Erstelle zwei weitere Dateien im Verzeichnis `C:\tomcat\isapi_redirect\conf\`:
    - **workers.properties**
    - **uriworkermap.properties**

## Schritt 3: Konfiguration der workers.properties

1. Öffne die Datei `workers.properties` im Verzeichnis `C:\tomcat\isapi_redirect\conf\` und füge folgende Konfiguration hinzu:
   ```properties
   worker.list=ajp13
   worker.ajp13.port=8009
   worker.ajp13.host=localhost
   worker.ajp13.type=ajp13
   ```

    - `worker.list`: Definiert den verwendeten Worker, hier `ajp13`.
    - `worker.ajp13.port`: Der Port, auf dem der AJP-Connector läuft (8009).
    - `worker.ajp13.host`: Der Host, auf dem Tomcat läuft, in diesem Fall `localhost`.

## Schritt 4: Konfiguration der uriworkermap.properties

1. Öffne die Datei `uriworkermap.properties` im Verzeichnis `C:\tomcat\isapi_redirect\conf\` und füge folgende Zuordnung hinzu:
   ```properties
   /bella/*=ajp13
   /*.jsp=ajp13
   ```

    - Diese Datei bestimmt, welche URLs über den AJP-Connector an Tomcat weitergeleitet werden.
    - Alle Anfragen an `/bella/*` und `/*.jsp` werden an den Worker `ajp13` weitergeleitet, also an Tomcat.

## Schritt 5: Konfiguration von IIS

1. Öffne den **IIS Manager**.

2. Navigiere zu der Website, die du an Tomcat weiterleiten möchtest, und füge den **ISAPI Redirector** hinzu:
    - Gehe zu **Handler Mappings** → **Add Module Mapping**.
    - Gib den Pfad zur Datei `isapi_redirect.dll` an, die sich im Verzeichnis `C:\tomcat\isapi_redirect\` befindet.

3. Navigiere zu **ISAPI Filters** und füge ebenfalls den ISAPI Redirector als neuen Filter hinzu:
    - Name: `TomcatConnector`
    - Executable: `C:\tomcat\isapi_redirect\isapi_redirect.dll`

4. Stelle sicher, dass der IIS-Server so konfiguriert ist, dass er Anfragen für dynamische Inhalte an Tomcat weiterleitet. Dies wird durch die `uriworkermap.properties` gesteuert.

## Schritt 6: Überprüfung der Konfiguration

1. Starte **IIS** und **Tomcat** neu, um sicherzustellen, dass alle Änderungen übernommen werden.

2. Teste die Verbindung, indem du eine JSP-Datei oder eine Anwendung, die auf Tomcat läuft, über IIS aufrufst.

   Beispiel:
   ```url
   http://localhost/bella/index.jsp
   ```

   Die Anfrage sollte an Tomcat weitergeleitet und die JSP-Datei korrekt ausgeliefert werden.

## Fehlerbehebung

1. **Tomcat-Logs**: Überprüfe die Logs unter `C:\tomcat\logs\` auf Fehlermeldungen.

2. **ISAPI Logs**: Überprüfe die Datei `isapi_redirect.log`, die im Verzeichnis `C:\tomcat\isapi_redirect\logs\` erstellt wird, um Informationen über Weiterleitungsfehler zu erhalten.

3. **Firewall und Ports**: Stelle sicher, dass der AJP-Port 8009 in der Firewall geöffnet ist und nicht von einer anderen Anwendung verwendet wird.

## Zusammenfassung

In dieser Anleitung hast du gelernt, wie du Microsoft IIS und Apache Tomcat mit Hilfe des AJP-Protokolls verbindest. Dies ermöglicht es, statische Inhalte über IIS und dynamische Inhalte wie JSP-Seiten oder Servlets über Tomcat zu bedienen. Durch die Trennung der Aufgaben zwischen den beiden Servern kann die Leistung deiner Webanwendung optimiert werden.
