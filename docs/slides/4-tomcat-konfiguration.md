# Konfiguration

--

## Konfigurations-Orte

| Datei                                 | Beschreibung                                                                                           |
|---------------------------------------|--------------------------------------------------------------------------------------------------------|
| conf/server.xml                       | Hauptkonfigurationsdatei                                                                               |
| conf/tomcat-users.xml                 | Benutzer- und Rollenkonfiguration                                                                      |
| conf/web.xml                          | Standard-Webanwendungskonfiguration, die für alle Webanwendungen gelten (Default Servlet, MIME-Types). |
| conf/catalina.properties              | Systemeinstellungen und Pfade                                                                          |
| conf/logging.properties               | Zentrale Log Konfiguration                                                                             |
| webapps/\<NAME\>/<br/>META-INF/context.xml | Kontextkonfiguration der Anwendung                                                                     |
|                                       |                                                                                                        |
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


--

## Connector: HTTP/1.1

```xml
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               maxParameterCount="1000"
               />
```
- Connectoren sind für die Kommunikation mit Clients verantwortlich und implementieren Protokolle wie HTTP, AJP oder HTTP/2.
- Kann sowohl HTTP als auch HTTPS Verbindungen unterstützen.


--

> **Note für den Sprecher:**
> Erläutern Sie den Unterschied zwischen HTTP und HTTPS Connectors. Führen Sie das Thema SSL/TLS ein und erklären Sie,
> wie man den HTTPS Connector mit einem SSL-Zertifikat konfiguriert.


--

## server.xml: Engine

- Verarbeitet eingehende HTTP-Anfragen.
- Verknüpft mit Hosts für die Ausführung von Webanwendungen.

Beispiel:

```xml
<Engine name="Catalina" defaultHost="localhost">
  <!-- Host Definition -->
  <Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true">
    <Context path="" docBase="ROOT" reloadable="true"/>
    <Context path="/app1" docBase="app1" reloadable="true"/>
  </Host>
</Engine>
```

---

> **Note für den Sprecher:**
> Weisen Sie darauf hin, dass eine Engine für mehrere Hosts verantwortlich ist, was das Hosten mehrerer Domänen
> ermöglicht. Erklären Sie, dass jeder `<Context>` eine Webanwendung innerhalb eines Hosts definiert.

---

## Host

- Definiert virtuelle Hosts (Domains) und die Webanwendungen, die darauf laufen.
- Mehrere Hosts ermöglichen Multi-Domain-Hosting.

Erweitertes Beispiel:

```xml
<Host name="example.com" appBase="webapps"
      unpackWARs="true" autoDeploy="true" xmlValidation="false">
      
  <!-- Webapps -->
  <Context path="/" docBase="ROOT" reloadable="true" crossContext="true"/>
  <Context path="/api" docBase="api" reloadable="true"/>
  
</Host>
```


> Note:
> Diskutieren Sie, wie das `appBase` Verzeichnis bestimmt, wo Tomcat nach Webanwendungen sucht. Erklären Sie, wie der
> Parameter `reloadable="true"` zur Entwicklung nützlich ist, um Anwendungen neu zu laden, ohne Tomcat neu starten zu
> müssen.

--

## Benutzerverwaltung

### Realms

- Authentifizierung und Autorisierung der Benutzer wird über Realms gesteuert.
- Der Realm kann Benutzerinformationen aus unterschiedlichen Quellen laden, z.B. aus einer Datenbank oder einem
  LDAP-Verzeichnis.

Erweitertes Beispiel für JDBCRealm:

```xml
<Realm className="org.apache.catalina.realm.JDBCRealm"
       driverName="com.mysql.cj.jdbc.Driver"
       connectionURL="jdbc:mysql://localhost:3306/tomcat_users"
       connectionName="root" connectionPassword="password"
       userTable="users" userNameCol="username" userCredCol="password"
       userRoleTable="user_roles" roleNameCol="rolename"/>
```

---

> **Note für den Sprecher:**
> Erläutern Sie den Vorteil von Realms und gehen Sie auf unterschiedliche Arten ein (MemoryRealm, JDBCRealm, JNDIRealm).
> Erklären Sie, dass Realms in Unternehmensumgebungen oft an LDAP oder Datenbanken angebunden werden.

---

### Rollen und Benutzer

- Benutzer werden in der Datei `tomcat-users.xml` definiert.
- Rollen ermöglichen eine feingranulare Steuerung des Zugriffs.

Erweitertes Beispiel:

```xml
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<user username="admin" password="secret" roles="manager-gui,admin-gui"/>
<user username="developer" password="devpass" roles="manager-script"/>
```

---

> **Note für den Sprecher:**
> Weisen Sie darauf hin, dass der Zugriff auf sensible Bereiche wie das Tomcat-Manager-Interface durch Rollen gesteuert
> wird. Veranschaulichen Sie die Wichtigkeit sicherer Passwörter.

---

## Sessions

- Sitzungen speichern den Zustand von Benutzern zwischen Anfragen.
- Die Konfiguration erfolgt meist in `context.xml`.

Erweitertes Beispiel:

```xml
<Context>
  <Manager className="org.apache.catalina.session.PersistentManager"
           saveOnRestart="true"
           maxActiveSessions="100">
           
    <Store className="org.apache.catalina.session.FileStore"
           directory="session_storage"/>
  </Manager>
</Context>
```

---

> **Note für den Sprecher:**
> Erläutern Sie, wie Tomcat Sitzungen verwaltet und warum eine persistente Speicherung sinnvoll sein kann. Führen Sie
> die Konfiguration von Session-Managern und Speichern ein.

---

## Server.xml

- Die Hauptkonfigurationsdatei von Tomcat.
- Enthält Definitionen für Services, Hosts und Connectors.

Erweitertes Beispiel:

```xml
<Server port="8005" shutdown="SHUTDOWN">
  <Service name="Catalina">
    <Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443"/>
    <Engine name="Catalina" defaultHost="localhost">
      <Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true">
        <Context path="" docBase="ROOT"/>
      </Host>
    </Engine>
  </Service>
</Server>
```

---

> **Note für den Sprecher:**
> Betonen Sie, dass `server.xml` die übergeordnete Konfigurationsdatei ist und bei Änderungen an dieser Datei Tomcat neu
> gestartet werden muss. Erklären Sie die Struktur dieser Datei und ihre Bedeutung.

---

## Context.xml

- Definiert Kontexteinstellungen für einzelne Webanwendungen.
- Wird typischerweise genutzt, um Datenquellen und Umgebungsvariablen zu definieren.

Erweitertes Beispiel:

```xml
<Context path="/myapp" docBase="myapp" reloadable="true">
  <Resource name="jdbc/MyDB" auth="Container"
            type="javax.sql.DataSource" driverClassName="com.mysql.cj.jdbc.Driver"
            url="jdbc:mysql://localhost:3306/mydb" username="root" password="password"
            maxTotal="20" maxIdle="10" maxWaitMillis="10000"/>
</Context>
```

---

> **Note für den Sprecher:**
> Weisen Sie auf die Flexibilität der `context.xml` hin, da jede Webanwendung ihre eigene Konfiguration haben kann.
> Erklären Sie, wie diese Datei das Laden von Datenquellen ermöglicht.

---

## Web.xml

- Definiert die Standard-Servlet-Konfiguration für alle Anwendungen.
- Jede Webanwendung kann eine eigene `web.xml` haben.

Erweitertes Beispiel:

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

---

> **Note für den Sprecher:**
> Erklären Sie, wie `web.xml` als Deployment-Deskriptor fungiert. Führen Sie aus, wie Servlet-Mappings konfiguriert
> werden und warum dies wichtig ist, um bestimmte URLs auf Servlets zu mappen.

---
