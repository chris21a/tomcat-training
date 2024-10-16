# Installation und Optimierung der Tomcat Sicherheit

In diesem Tutorial wird die Installation von Apache Tomcat überprüft und anhand von bewährten Sicherheitspraktiken optimiert. Die Schritte werden detailliert erklärt, um sicherzustellen, dass Tomcat den höchsten Sicherheitsstandards entspricht.

> **Dokumentation:**  
> [Apache Tomcat 9 Security How-To](https://tomcat.apache.org/tomcat-9.0-doc/security-howto.html)

---

## Voraussetzung

- Tomcat ist installiert und als Dienst mit dme User  `tomcat` eingerichtet

---

## Berechtigungen und Dateiabsicherung

- Stellen Sie sicher, dass Tomcat nur die **notwendigen Berechtigungen** besitzt.
    - **Verzeichnisrechte**: Die Verzeichnisse `conf`, `webapps`, `logs`, `temp`, und `work` sollten nur dem Tomcat-Benutzer zugänglich sein.
    - **Dateiberechtigungen**: Die Datei `server.xml` und andere Konfigurationsdateien sollten nur lesbar sein.


---

## Netzwerk und Firewall

- Richten Sie eine **Firewall** ein, die nur die notwendigen Ports für Tomcat öffnet, z. B. Port 8080 für HTTP und Port 8443 für HTTPS.
- **Verbindungen absichern**: Schließen Sie ausgehende Verbindungen und beschränken Sie den Zugriff auf den Server über Remote-Dienste wie JMX.

> Nicht relecant in der Trainings-Umgebung 


---

## Sicherheitskonfiguration von Tomcat

### JMX und Standard-Webanwendungen

- **Standard-Webanwendungen entfernen**: Webanwendungen wie `ROOT`, `examples`, `docs` und `host-manager` sollten entfernt oder deaktiviert werden.
- **Manager-App absichern**: Die Manager-Anwendung erlaubt das Deployment und sollte nur mit starken Passwörtern und IP-Whitelist gesichert sein.

---

## Anpassung der `server.xml`

- Entfernen Sie alle **ungültigen oder ungenutzten Komponenten** aus der Datei `server.xml`.
- **Shutdown-Port deaktivieren**: Stellen Sie den Wert des `shutdown` Ports auf `-1`, um den Shutdown-Port zu deaktivieren, oder verwenden Sie ein starkes Passwort.
- **AJP-Protokoll**: Verwenden Sie AJP nur in vertrauenswürdigen Netzwerken, da es ein Klartext-Protokoll ist.

---

## Konfiguration von Hosts, Contexts und Valves

- **AutoDeploy deaktivieren**: Stellen Sie sicher, dass `autoDeploy` und `deployOnStartup` deaktiviert sind, um unautorisiertes Deployment zu verhindern.
- **AccessLogValve aktivieren**: Aktivieren Sie den Access Log Valve, um Zugriffe auf den Server zu protokollieren.
- **ErrorReportValve anpassen**: Entfernen Sie Informationen zur Tomcat-Version aus Fehlermeldungen, um potenziellen Angreifern keine Informationen preiszugeben.
- **IP-basierte Zugriffsbeschränkungen**: Verwenden Sie die `RemoteAddrValve`, um den Zugriff auf den Server auf bestimmte IP-Adressen zu beschränken.

---

## Sicherheitskonfiguration für Realms

- **Starke Sitzungssicherheit**: Verwenden Sie eine ausreichend große `sessionIdLength` und starke Zufallsgeneratoren (`randomClass`), um sichere Sitzungs-IDs zu erzeugen.
- **Session-Cookies absichern**: Stellen Sie sicher, dass Sitzungs-Cookies nur über **SSL** gesendet werden.

---

## Absicherung temporärer Dateien und Dateiuploads

- Temporäre Dateien sollten in gesicherten Verzeichnissen gespeichert werden, die nur dem Tomcat-Benutzer zugänglich sind.
- Dateiuploads sollten in separaten Verzeichnissen gesichert und überprüft werden, um potenziellen Missbrauch zu verhindern.

