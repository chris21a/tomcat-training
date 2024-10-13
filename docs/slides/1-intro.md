# Apache Tomcat
![Apache_Tomcat_logo.svg.png](img%2FApache_Tomcat_logo.svg.png)
* Free and Open Source
* [Apache License v2](https://www.apache.org/licenses/LICENSE-2.0)
Note:
- Tomcat wurde von James Duncan Davidson benannt
- Er war 1997 bis 2001 Entwickler bei Sun Microsystems und war maßgeblich an der Entwicklung von Tomcat und Ant beteiligt
- Ein Kater ist das namensgebende Tier. Der Gedanke war, dass der Server wie ein „freilaufender Kater“ (Tomcat) robust und unabhängig sein sollte.
* Die Apache Software Foundation wurde von den Entwicklern des Apache Webservers gegründet als Organisation, um Open Source Software Projekte zu fördern. Heute gibt es viele 100 Projekte, die von der ASF gefördert werden.
* Die Apache v2 Lizenz erlaubt die Nutzung, Modifikation und Weitergabe, auch unter anderen Lizenten und ohne das der Code offengelegt werden muss


--

## Webserver: Der Maschinenraum des WWW
![overview-client-server.png](img%2Foverview-client-server.png)
- Nur durch Webserver, die leistungsfähig und zuverlässig einsetzbar sind, konnte das World Wide Web die enorme Verbreitung erlangen.
- Ende der 1990er / Anfang 2000 fanden viele Innovationen auf diesem Gebiet statt
  - Der Apache Webserver wurde als Open Source zum meistgenutzten Webserver
  - Sun erfand Java und baute leistungsfähige Unix Server
  - Dynamische Webanwendungen gewannen an Bedeutung. Die Servlet-Spezifikation und Java Server Pages waren geeignete Technologien.
  

--

## Webserver bearbeiten viele Anfragen parallel

![overview-worker-threads.gif](img%2Foverview-worker-threads.gif)
- Webserver halten gleichzeitig viele Verbindungen offen
- Jede Anfrage wird in einem eigenen Thread bearbeitet
- Threads sind leichtgewichtige Prozesse, die von der CPU des Servers abgearbeitet werden


--

## Tomcat: Servlet-Container und JSP-Engine

Tomcat war die Referenzimplementierung der Java Servlet- und JSP-Spezifikationen

--

## Servlet-API

```java

import javax.servlet.*;

public class HelloServlet extends HttpServlet {

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
        response.setContentType("text/html");
        response.getWriter().println("<h1>Hello, Servlet API!</h1>");
        response.getWriter().println("<p>Aktuelle Zeit:" + new java.util.Date()+"</p);
    }

}

```
- Abstrahiert die HTTP-Protokollschicht (GET, POST, DELETE, ...)
- Ermöglicht die Verwaltung von Sessions, Cookies, Header, ...
- Plattformunabhängig, da in Java geschrieben
- Multi-Threaded, da für parallele Anfragen ausgelegt
- Modular und Erweiterbar durch Interfaces und Klassen


--

## Java Server Pages (JSP)

```jsp

<%@ page import="javax.servlet.*, javax.servlet.http.*, javax.servlet.jsp.*" %>
<html>
  <body>
    <h1>Hello, JSP!</h1>
    <%= "Aktuelle Zeit: " + new java.util.Date() %>
  </body>
</html>

```
- Ermöglicht die Einbettung von Java-Code in HTML
- Einfacher Weg um Servlets mit viel HTML zu programmieren
- Wird zur Laufzeit in Servlets übersetzt


--

## Was Tomcat kann

- Servlet API
- JSP (Jakarta Server Pages)
- WebSockets
- Expession Language (EL)
- SSL/TLS Support
- Clustering, Session Replikation, Loadbalancing (mod_jk)
- JNDI
- JMX Management
- WAR-Deployment
- Hot Deployment
- Access Logging
- Static File Serving
- HTTP/2 und HTTP/3 Support
- Plattformunabhängig (Linux, Windows, MacOS, Unix)


--

## Weitere Features

- Virtual Hosts
- Valves
- Manager Application
- Security Realms und User Management
- Connection Pooling (JDBC)
- GZIP Compression
- Rewrites, CGI, SSI in Apache Syntax
- Embeddable (Spring Boot, Quarkus)
- Docker Images


--

## Was Tomcat nicht kann

- Java EE / Jakarta EE Standards:
  - Enterprise Java Beans (EJB)
  - Transcations (JTA), Contexts und Dependency Injection (CDI)
  - Persistence API (JPA), Messaging (JMS)
  - Batch Processing (JBatch), Mail (JavaMail), Connector Architecture (JCA)
  - Web Services (JAX-WS, JAX-RS)
  - Security (JAAS, JASPIC)
- Erweiterte Clustering- und Load-Balancing
- Zentrale Verwaltung und Konfiguration
- Reverse Proxy und SSL-Terminierung
- High Performance Static File Server
- Response Caching


> Viele Jakarta EE Standards können durch zusätzliche Bibliotheken oder alternative Frameworks wie Spring Boot ergänzt werden.


--

## Andere Web-Server

- Reine Webserver wie NGINX, Apache HTTPD oder MS IIS wurden für statische Inhalte und Reverse Proxying entwickelt.
- Statische Dateien werden schneller und effizienter ausgeliefert.
- Reverse Proxying ermöglicht das Weiterleiten von Anfragen an verschiedene Backend-Server.
- Diese Webserver können auch als Load-Balancer, SSL-Terminator und Cache-Server eingesetzt werden.
- Dise Webserver sind effizienter weil sie direkt sehr nah am Betriebssystem in C oder C++ geschrieben sind und weniger Overhead haben.
- Sie sind jedoch nicht geeignet um komplexe Anwendungen zu verarbeiten, die dynamische Inhalte generieren.
- Tomcat wird oft in Kombination mit einem Webserver eingesetzt, um statische Inhalte zu servieren und dynamische Inhalte zu generieren.


--

## Bedeutung von Tomcat für die Java Community

- Tomcat war einer der ersten Open Source Servlet-Container und JSP-Engines
- Es hat die Entwicklung von Java Webanwendungen und Services maßgeblich beeinflusst
- Tomcat hat die Akzeptanz von Java im Web-Bereich gestärkt
- Tomcat ist die erste Wahl für die Entwicklung von Webanwendungen in Java
- Es ist ein wichtiger Bestandteil vieler Java-Frameworks und Anwendungen
- Es ist kostenlos und ausgereift, mit einer großen Community und Unterstützung.


--

## Wer setzt Tomcat ein?

- Generell Java-basierte Web-Anwendungen und Services
- Frameworks wie Spring Boot, Struts, JSF, Vaadin
- Cloud-Plattformen wie AWS, Azure, Google Cloud
- Enterprise-Software wie Jira, Confluence, Jenkins, Solr, Elasticsearch
- CMS-Systeme wie Magnolia, Hippo, Jahia, FirstSpirit, CoreMedia, OpenCMS
- Portal-Server wie Alfresco, Liferay


--

## Tomcat Architektur
| Server    | Repräsentiert die gesamte Instanz. Enthält alle Konfigurationen und Ressourcen.                                       |
|-----------|-----------------------------------------------------------------------------------------------------------------------|
| Service   | Bindet mehrere Connectoren an eine Engine. Ein Server kann mehrere Services haben.                                    |
| Engine    | Stellt die Pipeline zur Verarbeitung von Anfragen für einen bestimmten Dienst dar.                                    |
| Host      | Verknüpfung eines Netzwerknamens, z. B. www.yourcompany.com, mit dem Server.                                          |
| Connector | Verwaltet die Kommunikation mit dem Client. Coyote für HTTP, AJP, HTTP/2                                              |
| Context   | Repräsentiert eine Webanwendung. Ein Host kann mehrere Kontexte enthalten, die jeweils einen eindeutigen Namen haben. |
| Webapp | Ein WAR-File, das eine Java.Webanwendung  enthält.                                                                    |


Note:
Eine Engine kann mehrere Hosts enthalten, und Host-Element unterstützt auch Netzwerk-Aliase                                                                                                                        
Es sind mehrere Konnektoren verfügbar: Coyote-Konnektor für HTTP-Verkehr, innsbesondere für Standalone-Server. JK2-Konnektor für Anbindung Apache Web Server oder IIS über AJP-Protokoll. Außerdem HTTP/2 Konnektor.                                    


--

## Tomcat Architektur im Bild
<img src="img%2Ftomcat-architecture.png" width="1000"/><br>

<sup>(Quelle: http://java-latte.blogspot.com/2014/10/introduction-to-architecture-of-apache-tomcat-with-server.xml.html)</sup>

--

## Tomcat Begriffe

| Catalina    | Die Servlet-Engine von Tomcat und Name des Standalone-Service.                                                 |
|-------------|----------------------------------------------------------------------------------------------------------------|
| Coyote      | Der HTTP-Connector von Tomcat                                                                                  |
| Jasper      | Die JSP-Engine von Tomcat                                                                                      |
| Jakarta     | Früher ein Apache-Top-Level-Projekt für Java-Projekte (Tomcat, Struts, Maven, Ant), jetzt Namensgeber für Eclipse Jakarta EE (vormals Java EE) |

Note:
* Catalina ist das Herzstück von Tomcat und führt die Servlets aus. Wenn eine Anfrage an Tomcat gesendet wird, verarbeitet Catalina diese und gibt die entsprechende Antwort zurück. Es fungiert als der "Motor" von Tomcat, indem es alle eingehenden HTTP-Anfragen bearbeitet und auf Grundlage der Servlet-Spezifikation arbeitet. In der Konfigurationsdatei *server.xml* ist Catalina der Hauptcontainer.
* Die gängigsten Connectoren sind der HTTP-Connector und der AJP-Connector. Coyote ist der Standard-HTTP-Connector von Tomcat und ist für die Verarbeitung von HTTP-Anfragen zuständig. Es unterstützt sowohl HTTP 1.1 als auch WebSockets und ermöglicht es Tomcat, sowohl als herkömmlicher Webserver als auch als Container für dynamische Webanwendungen zu dienen. Coyote wird ebenfalls in der *server.xml* konfiguriert und ist sehr leistungsfähig bei der Verarbeitung von hohen Datenverkehrsmengen.
* Jasper ist die JSP-Engine von Tomcat und dient dazu, JSP-Seiten in Java-Servlets zu übersetzen. JSPs ermöglichen das Schreiben von dynamischen Webseiten mit Java, und Jasper kümmert sich darum, diese zur Laufzeit in ausführbare Servlets umzuwandeln. Sobald eine JSP in ein Servlet übersetzt wurde, wird sie von Catalina ausgeführt. Jasper sorgt dafür, dass Änderungen an JSP-Dateien automatisch erkannt und neu kompiliert werden.


--

## Tomcat Alternativen

| Jetty           | OS       | Sehr leichtgewichtig, schnell Embedded. Eclipse Foundation.                | 
|-----------------|----------|----------------------------------------------------------------------------|
| WildFly         | OS       | Enterprise Application Server von Red Hat, basierend auf JBoss.            | 
| Glassfish       | OS       | Referenzimplementierung von Jakarta EE. Eclipse Foundation.                |
| Apache TomEE    | OS       | Tomcat mit Jakarta EE Unterstützung.                                       |
| Payara          | OS/payed | Fork von Glassfish, mit erweiterten Funktionen und Support.                |
| Resin           | OS/payed | Kommerzieller Java Application Server, schnell und leistungsfähig.         |
| Red Hat JBoss   | payed    | Enterprise Application Platform von Red Hat, basierend auf WildFly.        |
| Oracle WebLogic | payed    | Enterprise Application Server von Oracle, unterstützt Jakarta EE Standards.|
| IBM WebSphere   | payed    | Enterprise Application Server von IBM, unterstützt Jakarta EE Standards.   |

--

## Tomcatund Java / Jakarta EE

> Enterprise Application Server wie JBoss, Glassfish werden höufig in größeren Unternehmen eingesetzt, wenn Jakarta EE (Java EE) Standards benötigt werden. Allerdings können auch diese Server mit Tomcat als Servlet-Container kombiniert werden.

> Tomcat ist leichgewichtiger und einfacher einsetzbar, sowohl in Produktion als auch Entwicklung.

> Aufgrund der Komplexität von Jakarta EE und des Vendor-Lockin verwenden viele Entwickler-Teams alternative Frameworks wie Spring Boot oder Quarkus und setzen diese zusammen mit Tomcat ein.

--

## Versionen

| Tomcat | Erscheinungsjahr | Java   | EE                                  |
|--------|------------------|--------|-------------------------------------|
| 11     | 2024             | 17     | Jarkarta EE; Servlet 6 & Pages 4.0  |
| 10     | 2022             | 11     | Jakarta EE: Servlet 6 & Pages 3.1 	|
| 9      | 2017             | 8 & 11 | Java EE 8: Servlet 4 / JSP 2.3      |
| 8      | 2014             | 7 & 8  | Java EE 7: Servlet 3.1 / JSP 2.3    |
| 7      | 2010             | 6 & 7  | Java EE 6: Servlet 3.0 / JSP 2.2    |

<br/>

- Neue Tomcat-Versionen passen sich den neuesten **Java EE/Jakarta EE** Standards an.
- Ältere Versionen von Tomcat sind nicht mit den neuesten Java-Versionen kompatibel.
- Der Wechsel von javax.* zu jakarta.* in Tomcat 10.x kann Migrationsaufwand erfordern.
- Hier: Tomcat 9

Note:
Apache Tomcat hat sich über die Jahre weiterentwickelt, um die neuesten Java- und Jakarta EE-Standards zu unterstützen. Tomcat 10.x ist besonders erwähnenswert, da es den Wechsel von der *javax.*-Namenskonvention zur *jakarta.*-Namenskonvention markiert, was eine bedeutende Änderung in der Entwicklung von Java-EE-basierten Anwendungen darstellt. Ältere Versionen wie Tomcat 9.x und 8.x sind noch weit verbreitet, insbesondere bei Anwendungen, die auf Java EE 8 oder älteren Standards basieren.

Jede Version von Apache Tomcat hat bestimmte Abhängigkeiten hinsichtlich der Java-Version. Tomcat 10.x benötigt mindestens Java 11, was ein Indikator für die Modernisierung von Anwendungen ist. Ältere Tomcat-Versionen wie 7.x und 8.x können auf älteren Java-Versionen laufen, was bei der Verwaltung älterer Anwendungen wichtig ist. Die Wahl der richtigen Java-Version ist entscheidend für die Stabilität und Sicherheit der Anwendung.

Besonders der Wechsel von javax.* zu jakarta.* in Tomcat 10.x bringt für Entwickler zusätzlichen Aufwand mit sich, da der gesamte Code und die Konfigurationen auf die neue Namenskonvention umgestellt werden müssen. Bei der Planung von Migrationsprojekten sollte dieser Aspekt besonders berücksichtigt werden, um potenzielle Schwierigkeiten zu vermeiden.


--

## Ausblick

- Tomcat 11 wurde in 2024 released und unterstützt Java 17 und Jakarta EE 9.
- Auf der Roadmap stehen Verbesserungen in den Bereichen Performance, Sicherheit und Stabilität.
  - Project Loom: Virtuelle Threads - Erhöhung des Durchsatz
  - HTTP/3 und QUIC (UDP, erhöhte Sicherheit und Geschwindigkeit, auch in Mobil- und WLAN-Netzen)
  - Verbesserung Garbage Collection (GC)

