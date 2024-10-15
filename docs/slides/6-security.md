# Security Considerations
<br>
<br>
<br>
<br>
<br>
<br>

> Dokumentation:
> https://tomcat.apache.org/tomcat-9.0-doc/security-howto.html

--

## Tomcat und Security
- <!-- .element: class="fragment" -->Tomcat ist standardmäßig sicher konfiguriert.
- <!-- .element: class="fragment" -->Tomcat sollte nicht unter dem Root-User laufen.
- <!-- .element: class="fragment" -->Dateiberechtigungen sollten eingeschränkt werden.
- <!-- .element: class="fragment" -->Firewall für eingehende und ausgehende Verbindungen verwenden.
- <!-- .element: class="fragment" -->Auto-Deploy sollte deaktiviert werden.
- <!-- .element: class="fragment" -->Logs, Temp und Work-Verzeichnisse sollten gesichert sein.
- <!-- .element: class="fragment" -->Passwörter für Benutzer und JMX sicher konfigurieren.


--

## JMX und Standard-Webanwendungen
- <!-- .element: class="fragment" -->JMX-Zugriff entspricht Root/Admin-Zugriff.
- <!-- .element: class="fragment" -->Nur interne Netzwerke für JMX-Zugriff verwenden.
- <!-- .element: class="fragment" -->Webanwendungen wie ROOT, Dokumentation, Beispiele sollten entfernt werden.
- <!-- .element: class="fragment" -->Die Manager App erlaubt Deployment und sollte gut gesichert sein oder auch entfernt werden.
- <!-- .element: class="fragment" -->Host Manager bietet virtuelle Host-Verwaltung, auch absichern oder entfernen.
- <!-- .element: class="fragment" -->Starke Passwörter für Management-Benutzer verwenden oder deaktivieren.
- <!-- .element: class="fragment" -->LockOutRealm verhindert Brute-Force-Angriffe (Sperrung von Benutzern bei zu vielen fehlgeschlagenen Login-Versuchen)
- <!-- .element: class="fragment" -->RemoteAddrValve sollte konfiguriert werden, um Zugriff per IP zu beschränken.


--

## Java Security Manager und server.xml
- <!-- .element: class="fragment" -->Java Security Manager begrenzt bösartige Aktionen von Webanwendungen.
- <!-- .element: class="fragment" -->Kann einige Angriffe nicht verhindern, wie hohe CPU-Last.
- <!-- .element: class="fragment" -->Extensive Tests notwendig, bevor der Security Manager verwendet wird.
- <!-- .element: class="fragment" -->server.xml: Entferne ungenutzte / auskommentierte Komponenten.
- <!-- .element: class="fragment" -->Port -1 deaktiviert den Shutdown-Port oder starke Passwörter für Shutdown-Port verwenden.
- <!-- .element: class="fragment" -->Entfernn nicht benötigte Connectoren.
- <!-- .element: class="fragment" -->AJP nur in vertrauenswürdigen Netzwerken verwenden da Klartext-Protokoll.


--

## Host, Context und Valves
- <!-- .element: class="fragment" -->AutoDeploy und deployOnStartup sollten deaktiviert werden.
- <!-- .element: class="fragment" -->crossContext sollte nur für vertrauenswürdige Anwendungen aktiviert werden.
- <!-- .element: class="fragment" -->AccessLogValve protokolliert Zugriffe und sollte aktiviert sein.
- <!-- .element: class="fragment" -->ErrorReportValve sollte angepasst werden, um Versionsinfos zu verbergen.
- <!-- .element: class="fragment" -->RewriteValve kann anfällig für ReDoS (Regular Expression Denial of Service) sein.
- <!-- .element: class="fragment" -->Zugriffslogs helfen bei der Fehlerbehebung und Überwachung.
- <!-- .element: class="fragment" -->sessionCookiePathUsesTrailingSlash kann Browsersicherheitsprobleme beheben.


--

## Realms und allgemeine Sicherheit
- <!-- .element: class="fragment" -->MemoryRealm sollte nicht in der Produktion verwendet werden.
- <!-- .element: class="fragment" -->JDBCRealm ist Single-Threaded, DataSourceRealm bevorzugt.
- <!-- .element: class="fragment" -->randomClass und sessionIdLength beeinflussen Sitzungs-IDs.
- <!-- .element: class="fragment" -->PersistAuthentication speichert authentifizierte Benutzer über Neustarts.
- <!-- .element: class="fragment" -->JDBCStore sollte gesichert sein, um Zugriff auf Sitzungsdaten zu verhindern.
- <!-- .element: class="fragment" -->Session-Cookies sollten über SSL gesichert werden.
- <!-- .element: class="fragment" -->java.io.tmpdir für temporäre Dateien sollte gesichert werden.
- <!-- .element: class="fragment" -->Dateien für Dateiuploads sollten in gesicherten Verzeichnissen gespeichert werden.

