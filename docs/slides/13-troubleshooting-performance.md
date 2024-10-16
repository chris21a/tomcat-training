# Troubleshooting & Performance


--

## Java-Kommandozeilen-Tools

### Überblick über wichtige JVM-Parameter

- **Garbage Collection Logging**: `-Xlog:gc`
    - Verfolgt die Ausführung der Garbage Collection
    - Beispiel: `java -Xlog:gc MyApp`

- **Speicherverbrauch überwachen**: `-Xmx` und `-Xms`
    - Steuert den maximalen und initialen Heap-Speicher
    - Beispiel: `java -Xms512m -Xmx2g MyApp`


--

## Performance-Analyse: CPU und Stacktraces

- **CPU-Last analysieren**: `jstat`
    - Zeigt JVM-Statistiken wie GC und Speicherallokationen an
    - Beispiel: `jstat -gcutil <PID> 1000 10` (alle 1s, 10 Mal)

- **Stacktraces erhalten**: `jstack`
    - Erstellt einen Thread-Dump der JVM
    - Beispiel: `jstack <PID> > stacktrace.txt`


--

## Speicher-Analyse: Heapdump und mehr

- **Heapdump erstellen**: `jmap`
    - Exportiert den Speicherinhalt zur Analyse
    - Beispiel: `jmap -dump:live,format=b,file=heapdump.hprof <PID>`

- **Heapgröße und Metadaten anzeigen**: `jmap -heap`
    - Beispiel: `jmap -heap <PID>`

- **GC-Probleme analysieren**: Heapdump-Dateien in Tools wie Eclipse MAT analysieren


--

<!-- Slide 1 -->
# Performance-Überwachung in Tomcat

- Leistungsmetriken: 
  - Request throughput
  - Memory usage
  - Thread utilization
  - Sessions.


--

# Anfrageverarbeitung in Tomcat

- Nutzt Akzeptor- und Arbeitsthreads.
- Threads werden in einem Pool effizient verwaltet.
- Konfigurationsoptionen:
    - **maxThreads**: Maximale Anzahl an Worker Threads.
    - **acceptCount**: Maximale Anzahl an Warteschlangen-Anfragen.
    - **minSpareThreads**: Minimale Anzahl an Leerlauf-Threads.


--

<!-- Slide 4 -->
# Überwachung wichtiger Metriken mit JMX

- **Anforderungsdurchsatz**: Gemessen durch requestCount und processingTime.
- **Sitzungsüberwachung**: Überwachen von activeSessions, maxActive, expiredSessions.
- **Thread-Pool-Auslastung**: currentThreadsBusy, maxThreads.
- **JVM-Speichernutzung**: Überwachen des Heap-Speichers: committed, used, init, max.


--

<!-- Slide 5 -->
# Tomcat-Überwachungstools

- **Tomcat Manager**: Einige Metriken sind über den Manager einsehbar
- **JConsole**: Desktop-Tools, im JDK enthalten
- **JMX Prometheus Exporter**: Generischer Exproter für MBeans: Prometheus JMX Exporter https://github.com/prometheus/jmx_exporter
- **Tomcat Prometheus-Exporter**: Einige "inoffizielle" Projekte, z.B https://github.com/nlighten/tomcat_exporter
- **JavaMelody**: Open-Source-Tool zur Überwachung von Metriken wie Speicher, Anforderungsdurchsatz, _JDBC-Verbindungen