# Tutorial: Java-Kommandozeilen-Tools Tutorial

## Voraussetzungen

- Finde die Prozess-ID (PID) der Java-Anwendung oder PID-Files des Tomcat:
- ```bash
  jps
  ```

---

## 1. Garbage Collection Logging aktivieren

### Schritt 1: GC-Logs in der JVM aktivieren

Führe deine Anwendung mit aktiviertem Garbage Collection Logging aus:
```bash
java -Xlog:gc MyApp
```

### Schritt 2: Beispielausgabe analysieren

```plaintext
[0.009s][info][gc] Using G1
[0.056s][info][gc] GC(0) Pause Young (Normal) (G1 Evacuation Pause) 9M->2M(256M) 5.849ms
```

**Analyse**:
- Die GC-Art wird verwendet (z.B. G1).
- Die Menge des freigegebenen Speichers wird angezeigt (von 9 MB auf 2 MB).

---

## 2. Speicherverbrauch konfigurieren und überwachen

### Schritt 1: Heap-Größe festlegen

Setze den minimalen und maximalen Heap-Speicher:
```bash
java -Xms512m -Xmx2g MyApp
```

### Schritt 2: Speicherverbrauch mit `jstat` überwachen

Verwende `jstat`, um die Speicherallokationen und GC-Statistiken zu überwachen:
```bash
jstat -gcutil <PID> 1000 10
```

### Beispielausgabe:

```plaintext
  S0     S1     E      O      M     CCS    YGC   YGCT   FGC    FGCT
  0.00  45.33  56.25  30.91  95.75  91.66     1    0.017   0      0.000
  0.00  45.33  80.47  30.91  95.75  91.66     1    0.017   0      0.000
```

**Analyse**:
- S0/S1: Survivor Spaces
- E: Eden Space
- O: Old Generation
- M: Metaspace

---

## 3. CPU-Last und Stacktraces analysieren

### Schritt 1: CPU-Nutzung überwachen

Führe `jstat` aus, um CPU-Statistiken für die JVM zu erhalten:
```bash
jstat -gc <PID> 1000 10
```

### Schritt 2: Thread-Dump mit `jstack` erstellen

Erstelle einen Stacktrace, um die Auslastung der Threads zu analysieren:
```bash
jstack <PID> > stacktrace.txt
```

### Beispielausgabe:
```plaintext
"main" #1 prio=5 os_prio=0 tid=0x0000000002d82800 nid=0x1a78 runnable [0x0000000002f8f000]
   java.lang.Thread.State: RUNNABLE
    at MyApp.run(MyApp.java:15)
    at ...
```

**Analyse**:
- Zeigt den Zustand jedes Threads und seinen aktuellen Stacktrace.

---

## 4. Heapdump erstellen und analysieren

### Schritt 1: Heapdump mit `jmap` erstellen

Erstelle einen Heapdump der laufenden Anwendung:
```bash
jmap -dump:live,format=b,file=heapdump.hprof <PID>
```

### Schritt 2: Heap-Informationen anzeigen

Nutze `jmap`, um eine Übersicht der Heap-Nutzung zu erhalten:
```bash
jmap -heap <PID>
```

### Beispielausgabe:

```plaintext
Attaching to process ID 12345, please wait...
Heap Configuration:
   MinHeapFreeRatio = 40
   MaxHeapFreeRatio = 70
   MaxHeapSize      = 2147483648 (2048.0MB)
   NewSize          = 13631488 (13.0MB)
   MaxNewSize       = 715653120 (682.5MB)
```

**Analyse**:
- Zeigt Konfiguration und Speichergröße des Heaps sowie der Young und Old Generation.

---

## 5. Garbage Collection Probleme analysieren

### Schritt 1: Analyse von Garbage Collection Zeiten

Mit dem Garbage Collection Log kannst du lange Pausenzeiten identifizieren:
```bash
-Xlog:gc*
```

### Schritt 2: Verwendung von Heapdump zur tiefgehenden Analyse

> Für Entwickler-Teams relevant


Importiere den erzeugten Heapdump (`heapdump.hprof`) in ein Analysetool wie [Eclipse MAT](https://www.eclipse.org/mat/):
- Ermittle Speicherlecks
- Analysiere die Referenzbaumstruktur


---

## Zusammenfassung

- **GC-Logs** helfen bei der Analyse von Speicherbereinigung.
- **Heapgröße** sollte angepasst werden, um OutOfMemoryErrors zu vermeiden.
- **Thread-Dumps** helfen bei der Identifikation von hängenden Threads.
- **Heapdumps** ermöglichen tiefgehende Speicheranalysen.

