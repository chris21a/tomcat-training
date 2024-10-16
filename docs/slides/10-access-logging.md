# Access Logging in Tomcat
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

> https://tomcat.apache.org/tomcat-9.0-doc/config/valve.html#Access_Logging


--

## Arten des Access Loggings in Tomcat
Access Logging basiert auf Valve-Komponenten in der `server.xml`.

- **AccessLogValve**: Standardventil für HTTP-Access-Logging in Tomcat.
- **Log File Rotation**: Automatische Rotation und Komprimierung von Log-Dateien.
- **Custom Valves**: Eigene Valves können implementiert werden.
- **JSON-basiertes Logging**: Logs im JSON-Format für strukturierte Daten.
- **Reverse Proxy Logs**: Proxys wie NGINX/Apache können auch als Access Logger fungieren.

--

## Konfiguration des logs-Ordners

- Standardmäßig speichert Tomcat Access Logs unter **`$CATALINA_BASE/logs/`**.
- **Umleitung in einen zentralen Ordner**:
  
    ```xml
    <Valve className="org.apache.catalina.valves.AccessLogValve"
           directory="/var/log/tomcat"
           prefix="access_log" suffix=".txt"
           pattern="%h %l %u %t &quot;%r&quot; %s %b"/>
    ```

- **Alternative: Symlink verwenden**:
  - Erstelle einen symbolischen Link vom Standard-Logs-Ordner zum zentralen Logs-Ordner:

    ```bash
    ln -s /var/log/tomcat /path/to/tomcat/logs
    ```

--

## AccessLogValve: Standard Logging

- **Konfiguration des AccessLogValve** in **`server.xml`**:
  
```xml
<Valve className="org.apache.catalina.valves.AccessLogValve"
   directory="logs"
   prefix="access_log" suffix=".txt"
   pattern="%h %l %u %t &quot;%r&quot; %s %b"
   rotatable="true"
   fileDateFormat=".yyyy-MM-dd"/>
```

- **Parameter**:
  - **`directory`**: Pfad zu den Logs.
  - **`pattern`**: Format für das Log (z.B. **NCSA**).
  - **`rotatable`**: Aktiviert die automatische Rotation.
  - **`fileDateFormat`**: Dateiformat für die Rotation.


--

# JsonAccessLogValve: JSON Logging

- **Konfiguration des JsonAccessLogValve** in **`server.xml`**:

    ```xml
    <Valve className="org.apache.catalina.valves.JsonAccessLogValve"
           directory="logs"
           prefix="access_log" suffix=".json"
           rotatable="true"
           fileDateFormat=".yyyy-MM-dd"
           requestAttributesEnabled="true"
           jsonPrettyPrint="false"/>
    ```

- **Parameter**:
    - **`directory`**: Pfad zu den JSON-Logs.
    - **`rotatable`**: Automatische Rotation der Log-Dateien.
    - **`requestAttributesEnabled`**: Schließt Anfrageattribute in das JSON-Log ein.
    - **`jsonPrettyPrint`**: Aktiviert die "hübsche" Formatierung des JSON-Logs.


--

## Log Rotation und Archivierung

- **Log Rotation**: Automatische Aufteilung der Log-Dateien nach Datum oder Größe.
  - Standardparameter für die Rotation im **AccessLogValve**:
    - **`rotatable="true"`**: Aktiviert die automatische Rotation.
    - **`fileDateFormat=".yyyy-MM-dd"`**: Setzt das Datumsformat für rotierte Dateien.
  
- **Externe Rotation Tools**:
  - Nutze externe Tools wie **logrotate**:
  
    ```bash
    /var/log/tomcat/*.txt {
      daily
      rotate 14
      compress
      missingok
      notifempty
    }
    ```

--

## Wichtige Punkte für Access Logging

- **Performance**: Logging kann die Serverleistung beeinflussen, insbesondere bei synchronem Logging.
- **Platzverwaltung**: Überwachung des Speicherplatzes für Logs, um Probleme zu vermeiden.
- **Sicherheitsaspekte**: Zugriff auf Logs einschränken (Berechtigungen, sensible Daten).
- **Anonymisierung**: Maskiere IP-Adressen und Benutzerdaten in Logs, um Datenschutzvorgaben einzuhalten.
- **Backup**: Implementiere eine Backup-Strategie für kritische Log-Dateien.
- **Log Monitoring**: Integration in Überwachungswerkzeuge (z.B. ELK, Splunk).
