# Tutorial: Anleitung zur Installation des Prometheus JMX Exporters für Tomcat

In diesem Tutorial lernst du, wie du den **Prometheus JMX Exporter** für Tomcat installierst, um JMX-Metriken zu exportieren und zu überwachen.

## 1. JMX Exporter herunterladen

- Besuche die [Prometheus JMX Exporter GitHub-Seite](https://github.com/prometheus/jmx_exporter).
- Lade die neueste Version des `jmx_prometheus_javaagent.jar` herunter. Dies ist die Datei, die als Java-Agent hinzugefügt wird.

```bash
wget https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.16.1/jmx_prometheus_javaagent-0.16.1.jar
```

## 2. Exporter im Tomcat installieren und konfigurieren

### a) Erstelle eine Konfigurationsdatei

- Lege eine Konfigurationsdatei (z.B. `jmx_exporter_config.yml`) an, um die MBeans und Metriken festzulegen, die exportiert werden sollen. Hier ein Beispiel:

```yaml
rules:
  - pattern: 'Catalina<type=ThreadPool,name="http-nio-8080"><>(.*)'
    name: 'tomcat_threadpool_$1'
    type: GAUGE
    help: 'Threadpool metrics for Tomcat'
  - pattern: 'java.lang<type=Memory><>(.*)'
    name: 'jvm_memory_$1'
    type: GAUGE
    help: 'JVM memory usage'
```

### b) Tomcat für den Exporter konfigurieren

- Öffne die Datei `catalina.sh` (Linux) oder `catalina.bat` (Windows) im Verzeichnis `bin/` deines Tomcat-Servers.
- Füge folgende Zeile am Anfang der Datei hinzu, um den JMX Exporter als Java-Agent hinzuzufügen:

```bash
JAVA_OPTS="$JAVA_OPTS -javaagent:/pfad/zu/jmx_prometheus_javaagent.jar=9404:/pfad/zu/jmx_exporter_config.yml"
```

- **Hinweis**: Ersetze `/pfad/zu/` durch den tatsächlichen Speicherort der heruntergeladenen `.jar`-Datei und der Konfigurationsdatei.

## 3. Tomcat neu starten

- Starte Tomcat neu, um den JMX Exporter zu aktivieren:

```bash
# Linux
./bin/shutdown.sh
./bin/startup.sh

# Windows
bin\shutdown.bat
bin\startup.bat
```

## 4. Exporter überprüfen durch Aufruf des Endpoints

- Sobald Tomcat läuft, kannst du die JMX Metriken durch den Aufruf des Endpoints prüfen:

```bash
curl http://localhost:9404/metrics
```

- Du solltest eine Liste von Metriken sehen, die im Prometheus-Format ausgegeben werden, z.B. Informationen zu Thread-Pools, Speicher und Anfragen.

## 5. Beschreibung Beispiel Ergebnis

Hier ist ein Beispiel für die Ausgabe des JMX Exporters:

```plaintext
# HELP tomcat_threadpool_currentThreadsBusy Threadpool metrics for Tomcat
# TYPE tomcat_threadpool_currentThreadsBusy gauge
tomcat_threadpool_currentThreadsBusy 5

# HELP jvm_memory_used JVM memory usage
# TYPE jvm_memory_used gauge
jvm_memory_used 1023424

# HELP tomcat_threadpool_maxThreads Maximum number of threads in Tomcat thread pool
# TYPE tomcat_threadpool_maxThreads gauge
tomcat_threadpool_maxThreads 200
```

- **tomcat_threadpool_currentThreadsBusy**: Die Anzahl der aktiven Threads im Tomcat-Thread-Pool.
- **jvm_memory_used**: Der aktuell verwendete Speicher des Java Virtual Machine (JVM).
- **tomcat_threadpool_maxThreads**: Die maximale Anzahl der Threads im Tomcat-Thread-Pool.

Diese Metriken können jetzt in **Prometheus** erfasst und überwacht werden.

---

Mit diesem Setup hast du den Prometheus JMX Exporter erfolgreich in Tomcat integriert und kannst nun die Metriken überwachen.
```

Dieses Tutorial führt Schritt für Schritt durch die Installation und Konfiguration des JMX Exporters für Tomcat.