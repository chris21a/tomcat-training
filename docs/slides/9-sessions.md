# Sessions
### Verwaltung und Konfiguration von Sessions in Tomcat

--

## Session Timeout

- **Definition**: Das Timeout gibt an, nach wie viel Inaktivität eine Session automatisch beendet wird.
- **Konfiguration**:
    - In der `web.xml` festgelegt:
      ```xml
      <session-config>
        <session-timeout>30</session-timeout>  <!-- Timeout in Minuten -->
      </session-config>
      ```
    - **Standardwert**: 30 Minuten, wenn kein Timeout angegeben ist.

--

## Session Persistenz

- **Ziel**: Sessions über Server-Neustarts hinweg speichern.
- **Konfiguration**: In der `context.xml` oder im `<Context>`-Element.
- Beispiel:
    ```xml
    <Context>
      <Manager className="org.apache.catalina.session.PersistentManager"
               saveOnRestart="true"
               maxActiveSessions="1000">
          <Store className="org.apache.catalina.session.FileStore" directory="sessions" />
      </Manager>
    </Context>
    ```
- **Erklärung**:
    - **`saveOnRestart`**: Speichert Sessions beim Herunterfahren.
    - **`maxActiveSessions`**: Begrenzung aktiver Sessions.

--

## Übersicht der Tomcat-Manager-Implementierungen

| **Manager**                                                  | **Funktion**                                              | **Verwendung**                                |
|-----------------------------------------------------------------------|-----------------------------------------------------------|-----------------------------------------------|
| **StandardManager**      | Standard-Session-Management im Speicher.                   | Standardmäßig, keine besondere Konfiguration. |
| **PersistentManager** | Speichert Sessions auf persistentem Speicher.              | Für Session-Persistenz über Neustarts hinweg. |
| **BackupManager**     | Synchronisiert Sessions zwischen Cluster-Nodes (Backup).   | In Clustering-Umgebungen.                     |
| **DeltaManager**      | Synchronisiert nur die Änderungen der Sessions (Deltas).   | Effiziente Replikation in Clustering-Umgebungen. |
| **NonBlockingManager**| Nicht blockierende Verwaltung von Sessions.                | Für hochgradig parallele Umgebungen.          |


--

## Session Cookie vs. URL Rewriting

- **Standard**: Tomcat verwendet Cookies (z.B. `JSESSIONID`), um Sessions zu verfolgen.
- **Konfiguration** von Session-Cookies:
    ```xml
    <Context sessionCookieName="MYSESSIONID" sessionCookiePath="/" />
    ```
    - **`sessionCookieName`**: Name des Session-Cookies.
    - **`sessionCookiePath`**: Pfad, auf dem das Cookie gültig ist.

- **URL Rewriting** als Fallback, wenn Cookies deaktiviert sind:
    - Web-Entwicklung muss das automatisches Anfügen der Session-ID an die URL im Java Code berücksichtigen:
      ```java
      // Java Code
      response.encodeURL("/myapp/index.jsp");
      ```

--

## Session ID

- **Zweck**: Jede Session erhält eine eindeutige ID.
- **Konfiguration der ID-Länge** im `<Context>`-Element:
    ```xml
    <Context sessionIdLength="32" />
    ```
    - **`sessionIdLength`**: Definiert die Länge der Session-ID (Standard: 16 Bytes).


--

<!-- Slide 6: Session Routing bei AJP und Loadbalancing -->
## Session Routing bei AJP und Loadbalancing

- **Problem**: Bei mehreren Tomcat-Instanzen muss eine Session auf den richtigen Server geleitet werden.
- **Lösung**: **Session Sticky Routing** mit dem AJP-Connector.
- **Konfiguration** des AJP-Connectors in `server.xml`:
    ```xml
    <Connector port="8009" protocol="AJP/1.3"
               redirectPort="8443"
               proxyName="mydomain.com"
               proxyPort="80"
               jvmRoute="node1" />
    ```
    - **`jvmRoute`**: Fügt der Session-ID einen Suffix hinzu (z.B. `JSESSIONID=abc123.node1`), um Anfragen an den richtigen Server zu leiten.


--

## Zusammenfassung

- **Session Timeout**: Konfiguriert das Timeout in der `web.xml`.
- **Session Persistenz**: Speichert Sessions über Neustarts im `<Context>`.
- **Session Cookie vs. URL Rewriting**: Cookies sind Standard, URL-Rewriting als Fallback.
- **Session ID**: Passe die ID-Länge im `<Context>`-Element an.
- **Session Routing**: Mit `jvmRoute` für Sticky Sessions bei AJP und Load Balancing.

