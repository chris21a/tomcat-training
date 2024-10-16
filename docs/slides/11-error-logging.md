# Error Logging in Tomcat
<br/>
<br/><br/><br/><br/><br/><br/>

> https://tomcat.apache.org/tomcat-9.0-doc/logging.html


--

## Logfile Architektur in Tomcat

- **Error Logs** sind zentral für die Überwachung von Fehlern und Problemen.
- Tomcat verwendet mehrere Logdateien für unterschiedliche Zwecke:
    - **catalina.out**: Hauptlog, das alle Systemausgaben und Fehler enthält.
    - **localhost_log**: Enthält Fehler und Ausgaben für Anfragen an den Host.
    - **catalina.log**: Logs für Serverereignisse, wie Start und Stop.

- Abgrenzung: Access Logs werden über **Valves** konfiguriert:
    - **localhost_access_log**: Dient nur für Zugriffsstatistiken (kein Error Logging).

> Alle Logs befinden sich normalerweise im Verzeichnis **`$CATALINA_BASE/logs/`**.


--

## Bedeutung von Error Logs für das Troubleshooting

- **Error Logs** bieten detaillierte Einblicke in Probleme mit dem Server und Anwendungen.
- Typische Problemfelder:
    - **Startup-Probleme**: Fehlkonfigurationen werden im `catalina.out`-Log sichtbar.
    - **Laufzeitfehler**: Fehler während der Ausführung von Anwendungen (Exceptions, Abstürze).
    - **Ressourcenprobleme**: Fehlende oder falsche Ressourcen (z. B. Dateien, Datenbanken).

### Beispiel: Fehleranalyse

- **NullPointerException**: Kann durch Fehler in der Anwendungslogik auftreten.
- **ServletException**: Wird bei einem Problem mit der Webanwendung ausgelöst.


--

## Unterschied zu Access Logging

- **Access Logging**:
    - Protokolliert Anfragen und Antworten des HTTP-Servers.
    - Fokus auf den **Verlauf der Zugriffe**, nicht auf Fehler.

- **Error Logging**:
    - Protokolliert **Fehler** und **Exceptions**, die während des Betriebs auftreten.
    - Enthält detaillierte Informationen wie **Stacktraces**, um die Ursachen von Problemen zu finden.


--

## Java Logging Architektur in Tomcat

- Tomcat verwendet die Java **`java.util.logging`** (JUL) API für das interne Logging.
- **LoggingHandler** schreiben Lognachrichten in verschiedene Ziele wie **Dateien**, **Konsole** oder **Syslog**.
- **Logger** sammeln Lognachrichten und leiten sie an entsprechende Handler weiter.
- Tomcat verwendet einen **eigenen Logger-Wrapper (JULI)**, um paralleles Logging in Umgebungen wie Web-Apps zu unterstützen.

### Hauptkomponenten:
- **Logger**: Verwaltet das Sammeln von Logdaten.
- **Handlers**: Leiten Logs in Dateien, auf die Konsole oder an externe Systeme.
- **Formatters**: Bestimmen das Format der Lognachrichten.


--

## Wo werden Error Logs konfiguriert?

- Die Konfiguration der **Error Logs** erfolgt über die Datei **`conf/logging.properties`**.
- Standardmäßig schreibt Tomcat Fehler in:
    - **catalina.out**: Haupt-Logdatei.
    - **localhost_log**: Logs von localhost-Anfragen.
    - **catalina.log**: Enthält allgemeine Tomcat-Serverereignisse und Fehler.

> Diese Dateien befinden sich im Verzeichnis **`$CATALINA_BASE/logs/`**.


--

## logging.properties

- **`$CATALINA_BASE/conf/logging.properties`** ist die zentrale Konfigurationsdatei für die Java Logging-Einstellungen in Tomcat.

### Aufbau von `logging.properties`:

- Definiert **Logger**, **Handler** und **Log-Level**.
- Weist Logger bestimmten Klassen oder Paketen zu.
- Kann einstellen welche Funktonen auf welchem Level loggen sollen

--

## Wo und wie werden Log-Level konfiguriert?

- Log-Level steuern, welche Art von Nachrichten geloggt werden. Typische Level:
    - **SEVERE**: Kritische Fehler.
    - **WARNING**: Warnungen.
    - **INFO**: Allgemeine Informationen.
    - **CONFIG**: Konfigurationsdetails.
    - **FINE**, **FINER**, **FINEST**: Debug-Informationen.

### Beispiel in `logging.properties`:

```properties
org.apache.catalina.level = INFO
org.apache.catalina.core.ContainerBase.[Catalina].[localhost].level = FINE
org.apache.coyote.level = WARNING
```

