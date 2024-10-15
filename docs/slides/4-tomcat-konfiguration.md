# Konfiguration

--

## Konfigurations-Orte

| Datei                                      | Beschreibung                                                                                           |
|--------------------------------------------|--------------------------------------------------------------------------------------------------------|
| conf/server.xml                            | Hauptkonfigurationsdatei                                                                               |
| conf/tomcat-users.xml                      | Benutzer- und Rollenkonfiguration                                                                      |
| conf/web.xml                               | Standard-Webanwendungskonfiguration, die für alle Webanwendungen gelten (Default Servlet, MIME-Types). |
| conf/catalina.properties                   | Systemeinstellungen und Pfade                                                                          |
| conf/logging.properties                    | Zentrale Log Konfiguration                                                                             |
| webapps/\<NAME\>/<br/>META-INF/context.xml | Context Decriptor der Anwendung - Variante in der Webapp                                               |
| conf/\<ENGINE\>/\<HOST\>/\<NAME\>.xml      | Context Decriptor der Anwendung - Variante ausserhalb der Webapp                                       |
| webapps/\<NAME\>/<br/>WEB-INF/web.xml      | Konfiguration der Anwendung                                                                            |

Note:
Default Servlet für alle Webanwendungen, Static Files, JSP-Servlet, MIME-Types, Welcome File, etc. werden in der conf/web.xml definiert.

--

## server.xml


```xml
<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="..." />
  <Listener className="..." />

  <GlobalNamingResources> ... </GlobalNamingResources>

  <Service name="Catalina"> ... </Service>
  <Service name="AnotherService"> ... </Service>
</Server>
```
- **Port:** Nummer des Port für den Shutdown-Befehl, -1 = deaktiviert (erfordert JSVC oder Win Service)
- **Address:** Optional - Interface auf dem der Shutdown-Befehl empfangen wird
- **Shutdown:** Befehl zum Herunterfahren des Servers
- **Listener:** reagieren auf Ereignisse des Servers 
- **GlobalNamingResources:** Globale JNDI-Ressourcen
- **Service:** Jede Service-Instanz kann mehrere Connectors und Engines enthalten.

Note:
Shutdown Port kann aus Sicherheitsgründen deaktiviert werden. Das Catalina Stop Skript funktioniert in dem Fall nicht. Daher sollte man dann den Service über den Apache Commons Deamon (JSVC) laufen lassen (oder als Windows Service). 


--

## server.xml: GlobalNamingResources

```xml
  <GlobalNamingResources>
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
    <Resource name="jdbc/MyDataSource" .../>
    <Resource name="mail/Session" .../>
    <Resource name="..." .../>
  </GlobalNamingResources>
```
- Standardmässig wird hier die UserDatabase definiert.
- User werden über die Datei tomcat-users.xml definiert. Wird benötigt für die Manager App
- Weitere globale JNDI-Ressourcen werden hier konfiguriert bei Bedarf. Alternativ in der context.xml je Webapp.

Note:
JNDI-Ressourcen, sind Beans, die vom Server verwaltet werden und von Anwendungen genutzt werden können. Beispiele sind Datenbankverbindungen, Mail-Sessions, oder andere zentrale Objecte, etc. Eine ähnliche Aufgabe erfüllen Spring Beans.


--

## tomcat-users.xml

```xml

<tomcat-users ...">
  <user username="admin" password="***" roles="manager-gui"/>
  <user username="robot" password="***" roles="manager-script"/>
  
  <role rolename="tomcat"/>
  <role rolename="role1"/>
  
  <user username="tomcat" password="***" roles="tomcat"/>
  <user username="both" password="***" roles="tomcat,role1"/>
  <user username="role1" password="***" roles="role1"/>
</tomcat-users>
```

- Rollen für die Manager Webapp:
  - manager-gui    - ermöglicht den Zugriff auf die HTML-GUI und die Statusseiten
  - manager-script - ermöglicht den Zugriff auf die HTTP-API und die Statusseiten
  - manager-jmx    - manager-jmx - ermöglicht den Zugriff auf den JMX-Proxy und die Statusseiten
  - manager-status - ermöglicht nur den Zugriff auf die Statusseiten

- Benutzer und Rollen können auch von anderen Anwendungen definiert werden.
- Weitere UserDatabases können per JDBC oder LDAP definiert werden.


--

## server.xml: Listener

```xml
<Server ...>
  <Listener className="..." />
  ...
</Server>
```

| Claas                             | Beschreibung                                                                                                    |  
|-----------------------------------|-----------------------------------------------------------------------------------------------------------------|
| VersionLoggerListener             | Loggt die Tomcat-Version und die verwendeten Bibliotheken.                                                      |
| SecurityListener                  | Führt Security Checks beim Start durch. Disabled by default                                                     |
| AprLifecycleListener              | Prüft das Vorhandensein der APR/nativen Bibliothek und lädt sie, wenn sie vorhanden ist. Für effizienteres SSL. |
| JreMemoryLeakPreventionListener   | Workaround für bekannte Stellen im Kontextklassenlader verwendet, die zu Speicherleck führen.                   |
| GlobalResourcesLifecycleListener  | Initialisiert globale JNDI Ressourcen aus der server.xml                                                        |
| ThreadLocalLeakPreventionListener | Workaround für Thread Local bedingte Speicherlecks                                                              |


--

## server.xml: Service

```xml
<Service name="Catalina">
  <Connector port="8080" protocol="HTTP/1.1" .../>
  <Connector port="8009" protocol="AJP/1.3" .../>

  <Engine name="Catalina" defaultHost="localhost">
    ...
  </Engine>
</Service>
```
- Ein Service besteht aus einer Kombination von Konnektoren und einer Engine.
- Der Default-Service ist "Catalina".
- Es sind mehre Services pro Server möglich, aber eher unüblich.

Note:
Mehrere Services sind möglich und erlauben eine multi-mandaten-föhigkeit. Ein Service kann z.B. für die Entwicklung und ein anderer für die Produktion genutzt werden.
Allerdings würde ich von diesem Pattern abraten und stattdessen mehre Tomcat-Instanzen verwenden.
Die Vorteile sind bessere Isoliation, bessere Skalierbarkeit und bessere Wartbarkeit. Der Overhead für weitere Instanzen kann bei den heute üblicherweise verfügbaren Ressourcen vernachlässigt werden.


--

## server.xml: Connectoren

```xml
<Service name="Catalina">
    <Connector port="..." protocol="..." ... />
    <Connector port="..." protocol="..." ... />
    ...
</Service>
```
Connectoren sind für die Kommunikation mit Clients verantwortlich:

| **Connector**        | **Protocol**                         | **Verwendung**                                                              |
|----------------|--------------------------------------------|-----------------------------------------------------------------------------|
| HTTP/1.1 NIO   | org.apache.coyote.http11.Http11NioProtocol | (Non-blocking I/O) Bessere Skalierbarkeit, Non-blocking I/O                 |
| HTTP/1.1 NIO2  | org.apache.coyote.http11.Http11Nio2Protocol| Verbesserte Version von NIO, unterstützt HTTP/2                             |
| HTTP/1.1 APR   | org.apache.coyote.http11.Http11AprProtocol | Nutzt native Bibliotheken für bessere Performance und SSL-Unterstützung     |
| AJP            | AJP/1.3                                    | Verbindet Tomcat mit Webservern wie Apache HTTP Server oder IIS             |
| HTTP/2         | org.apache.coyote.http2.Http2Protocol      | Kein eigener Connector, verwendet Upgrade-Direktive                         |


--

## Allgemeine Connector Attribute 1/2

| **Attribut**              | **Beschreibung**                                             | **Standardwert**    |
|---------------------------|--------------------------------------------------------------|---------------------|
| **ajpFlush**               | Aktiviert/deaktiviert AJP-Flush-Nachrichten.                 | true                |
| **allowTrace**             | Aktiviert/deaktiviert die TRACE HTTP-Methode.                | false               |
| **asyncTimeout**           | Timeout für asynchrone Anfragen (ms).                        | 10000               |
| **enableLookups**          | DNS-Lookups für Clientnamen durchführen.                     | false               |
| **encodedSolidusHandling** | Handhabung von `%2f` in URLs steuern.                        | reject              |
| **maxHeaderCount**         | Maximale Anzahl von Headern pro Anfrage.                     | 100                 |
| **maxParameterCount**      | Maximale Anzahl von GET/POST-Parametern.                     | 10000               |
| **maxPostSize**            | Maximale Größe von POST-Anfragen (Bytes).                    | 2097152             |
| **maxSavePostSize**        | Maximale Größe von POST-Daten bei Authentifizierung.         | 4096                |
| **parseBodyMethods**       | HTTP-Methoden für Body-Parsing.                             | POST                |


--

## Allgemeine Connector Attribute 2/2

| **Attribut**              | **Beschreibung**                                             | **Standardwert**    |
|---------------------------|--------------------------------------------------------------|---------------------|
| **port**                   | TCP-Port für eingehende Verbindungen.                       | 8080 oder 8009 (AJP)|
| **protocol**               | Protokoll zur Verarbeitung des Verkehrs (AJP/HTTP).         | HTTP/1.1 oder AJP/1.3|
| **proxyName**              | Servername in Proxy-Konfiguration.                          | leer                |
| **proxyPort**              | Serverport in Proxy-Konfiguration.                          | 0                   |
| **redirectPort**           | Port für automatische SSL-Weiterleitung.                    | 8443                |
| **scheme**                 | Name des zurückgegebenen Protokolls (z. B. "https").        | http                |
| **secure**                 | Kennzeichnet die Anfrage als sicher (true/false).           | false               |
| **URIEncoding**            | Zeichenkodierung für URI-Dekodierung.                       | ISO-8859-1          |
| **useBodyEncodingForURI**  | Verwendet contentType-Kodierung für URIs.                   | false               |
| **useIPVHosts**            | Verwendet IP-Adresse für virtuelle Hosts.                   | false               |
| **xpoweredBy**             | Aktiviert/deaktiviert den X-Powered-By-Header.              | false               |


--

## Connector: HTTP/1.1

> Dokumentation: https://tomcat.apache.org/tomcat-9.0-doc/config/http.html

```xml
    <Connector port="8080" protocol="HTTP/1.1" 
      connectionTimeout="20000" redirectPort="8443" maxParameterCount="1000"/>

```

| **Parameter**        | **Beschreibung**                                                   | **Standardwert** |
|----------------------|--------------------------------------------------------------------|------------------|
| `port`               | Der Port, auf dem der Connector lauscht                            | 8080             |
| `protocol`           | HTTP/1.1 wählt Variante von Nio, Nio2 oder Apr                     | "HTTP/1.1"       |
| `connectionTimeout`  | Millisek. bevor inaktive Verbindung abbricht.                      | 60000 ms         |
| `redirectPort`       | Port, auf den Anfragen umgeleitet werden bei HTTPS                 | 8005              |
| `maxThreads`         | Maximale Größe des Thread Pool.                                    | 200              |
| `minSpareThreads`    | Anzahl Threads im Leerlauf                                         | 10               |
| `acceptCount`        | Maximale Anzahl Verbindungen in Warteschlange.                     | 100              |
| `keepAliveTimeout`   | Millisek. bevor Keep-Alive-Verbindung abbricht                     | 20000 ms         |
| `compression`        | Komprimierung von Inhalten. `on`, `off` oder `force`               | `off`            |
| `maxHttpHeaderSize`  | Maximale Größe HTTP-Header                                         | 8192 Bytes       |


--


## Connector: AJP/1.3

Connector für Anbindung Apache Webserver oder MS ISS: https://tomcat.apache.org/tomcat-9.0-doc/config/ajp.html

```xml
   <Connector protocol="AJP/1.3" address="::1" port="8009"
               redirectPort="8443" maxParameterCount="1000" />
```

| **Parameter**        | **Beschreibung**                                                   | **Standardwert**  |
|----------------------|--------------------------------------------------------------------|-------------------|
| `port`               | Der Port, auf dem der AJP Connector lauscht                        | 8009              |
| `protocol`           | Das verwendete Protokoll (AJP/1.3)                                 | "AJP/1.3"         |
| `redirectPort`       | Port, auf den Anfragen umgeleitet werden bei HTTPS                 | Kein Standardwert |
| `secretRequired`     | Erfordert ein geheimes Passwort für den AJP-Verbindungssicherheit  | `true`            |
| `maxThreads`         | Maximale Anzahl von Threads, die Anfragen gleichzeitig verarbeiten | 200               |
| `minSpareThreads`    | Mindestanzahl von Threads, die im Leerlauf vorgehalten werden      | 10                |
| `acceptCount`        | Maximale Anzahl von Verbindungen in der Warteschlange              | 100               |
| `connectionTimeout`  | Zeit (in Millisekunden), nach der inaktive Verbindungen getrennt werden | Kein Standardwert  |
| `keepAliveTimeout`   | Millisekunden, bevor eine Keep-Alive-Verbindung getrennt wird      | -1 (keine Begrenzung) |
| `address`      | Bind Adresse bei mereren Interfaces                        | localhost        |


--

## Connector: HTTP/2
> Dokumentation: https://tomcat.apache.org/tomcat-9.0-doc/config/http2.html

````xml

<UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />

````

--

## server.xml: Engine
- Die „Engine“ stellt die gesamte Maschinerie zur Verarbeitung von Anfragen dar, die mit einem Catalina-Service verbunden ist. 
- Empfängt und verarbeitet alle Anfragen von den Konnektoren und sendet die fertige Antwort an den Konnektor zurück.
> Documentation: https://tomcat.apache.org/tomcat-9.0-doc/config/engine.html

```xml

<Engine name="Catalina" defaultHost="localhost">
  <Cluster .../>
  
  <Realm .../>
  
  <Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true"> ... </Host>
  <Host name="www.acme.com" appBase="webapps-acme" unpackWARs="true" autoDeploy="true"> ... </Host>
</Engine>

```
- jvmRoute: Set Route to a unique value for sticky sessions


Note:
Engine ist für mehrere Hosts verantwortlich ist, was das Hosten mehrerer Domänen ermöglicht. 
Jeder `<Context>` definiert eine Webanwendung innerhalb eines Hosts.


--

## Host

- Definiert virtuelle Hosts (Domains) und die Webanwendungen, die darauf laufen.
- Alternativ Host Name Alias
> Dokumentation: https://tomcat.apache.org/tomcat-9.0-doc/config/host.html

```xml
<Host name="localhost" appBase="webapps"
      unpackWARs="true" autoDeploy="true" createDirs="true">
      
      <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs" ... />            
      
      <!-- nicht mehr üblich: 
      <Context path="" docBase="ROOT" reloadable="true" crossContext="true"/> 
      -->
</Host>
```

--

## Host Alias

- Anstatt eines neuen virtuellen Host mit eigenen Webapps kann auch ein Alias definiert werden

```xml
<Host name="localhost" appBase="webapps"
      unpackWARs="true" autoDeploy="true" createDirs="true">
      
      <Alias>www.acme.de</Alias>
      <Alias>www.acme.at</Alias>
      <Alias>www.acme.pl</Alias>
</Host>
```


--

## Context.xml

- Definiert Kontexteinstellungen für einzelne Webanwendungen.
- Wird typischerweise genutzt, um Datenquellen und Umgebungsvariablen zu definieren.
> Dokumentation: https://tomcat.apache.org/tomcat-9.0-doc/config/context.html

```xml
<Context path="/myapp" docBase="myapp" reloadable="true">
  <Resource name="jdbc/MyDB" auth="Container"
            type="javax.sql.DataSource" driverClassName="com.mysql.cj.jdbc.Driver"
            url="jdbc:mysql://localhost:3306/mydb" username="root" password="password"
            maxTotal="20" maxIdle="10" maxWaitMillis="10000"/>
</Context>
```


--

## Web.xml

- Definiert die Standard-Servlet-Konfiguration für alle Anwendungen.
- Jede Webanwendung kann eine eigene `web.xml` haben.

> Basic Example: https://tomcat.apache.org/tomcat-9.0-doc/appdev/web.xml.txt

```xml
<web-app>
  <servlet>
    <servlet-name>MyServlet</servlet-name>
    <servlet-class>com.example.MyServlet</servlet-class>
  </servlet>
  
  <servlet-mapping>
    <servlet-name>MyServlet</servlet-name>
    <url-pattern>/myservlet</url-pattern>
  </servlet-mapping>
</web-app>
```

Note:
Viele Einstellungen wie Servlet-Mappings werden mittlerweile häufig über Annotations direkt im Quellcode verwaltet.


--

## Struktur einer Webapp / WAR-Archiv

- Beispiel-Struktur einer Webapp mit dem Namen `my-webapp`.
- WAR-Datei: `my-webapp.war` (ZIP-komprimiert)

```text

my-webapp/
├── index.html
├── anotherpage.jsp
├── static/           # nur ein Beispiel, static Files können auch in anderen Ordnern liegen                        
│   ├── styles.css
│   ├── scripts.js
│   └── logo.png
├── WEB-INF/          # geschütztes Verzeichnis, seit Servlet 3.0 optional
│   ├── web.xml       # web.xml mittlerweile optional, Alternative sind Annotationen im Java Code
│   ├── classes/
│   │   └── com/
│   │       └── example/
│   │           └── MyServlet.class
│   ├── lib/
│   │   ├── library1.jar
│   │   └── library2.jar
│   └── tld/
│       └── custom-taglib.tld
└── META-INF/         # geschütztes Verzeichnis, optional
    └── context.xml   # Context kann auch an anderer Stelle definiert werden.

```
<!-- .element: class="r-stretch" -->


--

## ROOT-Webanwendung in Tomcat

- **ROOT** ist die Standard-Webanwendung in Tomcat.
- Sie wird geladen, wenn keine spezifische URL für eine andere Webanwendung angegeben ist.
- Wird verwendet, um eine Webseite direkt unter der Domain zu hosten (z. B. `http://example.com`).
- Man kann eine eigene Anwendung unter dem Namen **ROOT.war** bereitstellen.
 
```text
$CATALINA_HOME
├── webapps/                    
└── ROOT/           
   ├── index.jsp
   ├── favicon.ico
   ├── ...
   └── WEB-INF/   
      └── web.xml
```


--

## Static File Serving
- Static File Serving wird über das DefaultServlet ermöglicht, das Standardmäßig in jeder Webapp enthalten ist:
- Das DefaultServlet wird überlicherweise global in `CATALINA_HOME/conf/web.xml` konfiguriert.

```code

<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee" ... >
    <servlet>
        <servlet-name>default</servlet-name>
        <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
        
        <init-param><param-name>debug</param-name><param-value>0</param-value></init-param>
        <init-param><param-name>listings</param-name><param-value>false</param-value></init-param>

```


--

# Welcome File

- Legt fest welches File angezeigt wird wenn die URL "/" aufgerufen wird.
- Default wird global in `CATALINA_HOME/conf/web.xml` konfiguriert.
```code
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee" ..>
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>  <!-- Erste Datei, die Tomcat sucht -->
        <welcome-file>index.htm</welcome-file>   
        <welcome-file>index.jsp</welcome-file>
```


--

# Mime Types

- Default wird global in `CATALINA_HOME/conf/web.xml` konfiguriert.

```code
    <mime-mapping>
        <extension>htm</extension>
        <mime-type>text/html</mime-type>
    </mime-mapping>
    <mime-mapping>
        <extension>html</extension>
        <mime-type>text/html</mime-type>
    </mime-mapping>
    <mime-mapping>
        <extension>pdf</extension>
        <mime-type>application/pdf</mime-type>
    </mime-mapping>
```


--


# Session Timeout

- Zeit bis eine inaktive Session ungültig wird.

```code
    <session-config>
        <session-timeout>30</session-timeout> <!-- Minutes -->
    </session-config>
```