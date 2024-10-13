## Apache Tomcat
* Free and Open Source
* [Apache License v2](https://www.apache.org/licenses/LICENSE-2.0)
Note:
* Tomcat wurde von James Duncan Davidson nach dem Tier benannt, weil es kraftvoll und schwer zu zähmen ist. Die Idee dahinter war, dass Tomcat eine robuste und flexible Lösung ist.
* Die Apache Software Foundation wurde von den Entwicklern des Apache Webservers gegründet als Organisation, um Open Source Software Projekte zu fördern. Heute gibt es viele 100 Projekte, die von der ASF gefördert werden.
* Die Apache v2 Lizenz erlaubt die Nutzung, Modifikation und Weitergabe, auch unter anderen Lizenten und ohne das der Code offengelegt werden muss

--

## Was Tomcat kann

- Servlet API
- JSP (Jakarta Server Pages
- WebSocket
- Expession Language (EL)
- SSL/TLS Support
- Clustering, Session Replikation, Loadbalancing (mod_jk)
- JNDI
- JMX MAnaagement
- WAR-Deployment
- Hot Deployment
- Access Logging
- Static File Server
- HTTP/2 und HTTP/3 Support

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

- Jakarta EE Standards:
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

## Tomcat Architektur
| Server    | Repräsentiert die gesamte Instanz. Enthält alle Konfigurationen und Ressourcen.                                                                                                                                                                                                                        |
|-----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Service   | Bindet mehrere Connectoren an eine Engine. Ein Server kann mehrere Services haben.                                                                                                                                                                                                                     |
| Engine    | Stellt die Pipeline zur Verarbeitung von Anfragen für einen bestimmten Dienst dar.  |
| Host      | Verknüpfung eines Netzwerknamens, z. B. www.yourcompany.com, mit dem Server. |
| Connector | Verwaltet die Kommunikation mit dem Client. Coyote für HTTP, AJP, HTTP/2   |
| Context   | Repräsentiert eine Webanwendung. Ein Host kann mehrere Kontexte enthalten, die jeweils einen eindeutigen Namen haben.                                                                                                                                                                                  |


Note:
Eine Engine kann mehrere Hosts enthalten, und Host-Element unterstützt auch Netzwerk-Aliase                                                                                                                        
Es sind mehrere Konnektoren verfügbar: Coyote-Konnektor für HTTP-Verkehr, innsbesondere für Standalone-Server. JK2-Konnektor für Anbindung Apache Web Server oder IIS über AJP-Protokoll. Außerdem HTTP/2 Konnektor.                                    


--

## Tomcat Architektur Bild
<img src="img%2Ftomcat-architecture.png" width="1000"/><br>

<sup>(Quelle: http://java-latte.blogspot.com/2014/10/introduction-to-architecture-of-apache-tomcat-with-server.xml.html)</sup>

--

## Tomcat Begriffe


| Catalina    | Die Servlet-Engine von Tomcat und Name des Standalone-Service.                                                                                 |
|-------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| Coyote      | Der HTTP-Connector von Tomcat                                                                                                                  |
| Jasper      | Die JSP-Engine von Tomcat                                                                                                                      |
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

## Wer setzt Tomcat ein?

- Generell Java-basierte Web-Anwendungen und Services
- Frameworks wie Spring Boot, Struts, JSF, Vaadin
- Cloud-Plattformen wie AWS, Azure, Google Cloud
- Enterprise-Software wie Jira, Confluence, Jenkins
- CMS-Systeme wie Magnolia, Hippo, Jahia, FirstSpirit, CoreMedia, OpenCMS
- Portal-Server wie Alfresco, Liferay


> Enterprise Application Server wie JBoss, Glassfish werden höufig in größeren Unternehmen eingesetzt, wenn Jakarta EE (Java EE) Standards benötigt werden. Allerdings können auch diese Server mit Tomcat als Servlet-Container kombiniert werden.

> Tomcat ist leichgewichtiger und einfacher Einsetzbar, sowohl in Produktion als auch Entwicklung.
> Aufgrund der Komplexität von Jakarta EE und des Venor-Lockin verwenden viele Entwickler alternative Frameworks wie Spring Boot oder Quarkus und setzen diese zusammen mit Tomcat ein. 

--

## Versionen
| Tomcat | Erscheinungsjahr | Java   | EE                                  |
|--------|------------------|--------|-------------------------------------|
| 11     | 2024             | 17     | Jarkarta EE; Servlet 6 & Pages 4.0  |
| 10     | 2022             | 11     | Jakarta EE: Servlet 6 & Pages 3.1 	|
| 9      | 2017             | 8 & 11 | Java EE 8: Servlet 4 / JSP 2.3      |
| 8      | 2014             | 7 & 8  | Java EE 7: Servlet 3.1 / JSP 2.3    |
| 7      | 2010             | 6 & 7  | Java EE 6: Servlet 3.0 / JSP 2.2    |
<br>
- Neue Tomcat-Versionen passen sich den neuesten **Java EE/Jakarta EE** Standards an.
- Ältere Versionen von Tomcat sind nicht mit den neuesten Java-Versionen kompatibel.
- Der Wechsel von javax.* zu jakarta.* in Tomcat 10.x kann Migrationsaufwand erfordern.
- Hier: Tomcat 9

Note:
Apache Tomcat hat sich über die Jahre weiterentwickelt, um die neuesten Java- und Jakarta EE-Standards zu unterstützen. Tomcat 10.x ist besonders erwähnenswert, da es den Wechsel von der *javax.*-Namenskonvention zur *jakarta.*-Namenskonvention markiert, was eine bedeutende Änderung in der Entwicklung von Java-EE-basierten Anwendungen darstellt. Ältere Versionen wie Tomcat 9.x und 8.x sind noch weit verbreitet, insbesondere bei Anwendungen, die auf Java EE 8 oder älteren Standards basieren.

Jede Version von Apache Tomcat hat bestimmte Abhängigkeiten hinsichtlich der Java-Version. Tomcat 10.x benötigt mindestens Java 11, was ein Indikator für die Modernisierung von Anwendungen ist. Ältere Tomcat-Versionen wie 7.x und 8.x können auf älteren Java-Versionen laufen, was bei der Verwaltung älterer Anwendungen wichtig ist. Die Wahl der richtigen Java-Version ist entscheidend für die Stabilität und Sicherheit der Anwendung.

Besonders der Wechsel von javax.* zu jakarta.* in Tomcat 10.x bringt für Entwickler zusätzlichen Aufwand mit sich, da der gesamte Code und die Konfigurationen auf die neue Namenskonvention umgestellt werden müssen. Bei der Planung von Migrationsprojekten sollte dieser Aspekt besonders berücksichtigt werden, um potenzielle Schwierigkeiten zu vermeiden.


</script>
</section>
<section>
<section data-markdown data-separator="^\n---\n$" data-separator-vertical="^\n--\n$">
<script type="text/template">
    ## Versionen und Java
    - **Tomcat 10.x**: **Java 11** oder höher. Jakarta EE 9, wesentliche Änderung ist der Wechsel von javax.* zu jakarta.*
    - **Tomcat 9.x**: **Java 8** und **Java 11**. Servlet 4.0 und JavaServer Pages 2.3 (Java EE 8-konform).
    - **Tomcat 8.x**: **Java 7** und **Java 8**. Servlet 3.1, JavaServer Pages 2.3 (Java EE 7-konform).
    - **Tomcat 7.x**: **Java 6** oder **Java 7**. Servlet 3.0 und JavaServer Pages 2.2 (Java EE 6-konform).

    ## Wichtig:
    - Neue Tomcat-Versionen passen sich den neuesten **Java EE/Jakarta EE** Standards an.
    - Ältere Versionen von Tomcat sind nicht mit den neuesten Java-Versionen kompatibel.
    - Der Wechsel von javax.* zu jakarta.* in Tomcat 10.x kann Migrationsaufwand erfordern.

    Note:
    Apache Tomcat hat sich über die Jahre weiterentwickelt, um die neuesten Java- und Jakarta EE-Standards zu unterstützen. Tomcat 10.x ist besonders erwähnenswert, da es den Wechsel von der *javax.*-Namenskonvention zur *jakarta.*-Namenskonvention markiert, was eine bedeutende Änderung in der Entwicklung von Java-EE-basierten Anwendungen darstellt. Ältere Versionen wie Tomcat 9.x und 8.x sind noch weit verbreitet, insbesondere bei Anwendungen, die auf Java EE 8 oder älteren Standards basieren.

    Jede Version von Apache Tomcat hat bestimmte Abhängigkeiten hinsichtlich der Java-Version. Tomcat 10.x benötigt mindestens Java 11, was ein Indikator für die Modernisierung von Anwendungen ist. Ältere Tomcat-Versionen wie 7.x und 8.x können auf älteren Java-Versionen laufen, was bei der Verwaltung älterer Anwendungen wichtig ist. Die Wahl der richtigen Java-Version ist entscheidend für die Stabilität und Sicherheit der Anwendung.

    Besonders der Wechsel von javax.* zu jakarta.* in Tomcat 10.x bringt für Entwickler zusätzlichen Aufwand mit sich, da der gesamte Code und die Konfigurationen auf die neue Namenskonvention umgestellt werden müssen. Bei der Planung von Migrationsprojekten sollte dieser Aspekt besonders berücksichtigt werden, um potenzielle Schwierigkeiten zu vermeiden.
