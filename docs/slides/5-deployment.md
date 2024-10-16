# Deployment

--

## Deployment von Web Applikationen

- Deployment ist der Prozess der Installation einer Webanwendung (WAR-File) auf dem Tomcat-Server.
- Deployment kann statisch (vor dem Start) oder dynamisch (während des laufenden Betriebs) erfolgen.
- Tomcat Manager App bietet eine Web-Oberfläche und einen Endpoint für das Deployment per HTTP.
- Parallele Deployments ermöglichen das Testen von neuen Versionen ohne Unterbrechung des laufenden Betriebs.

```shell
# Copy local WAR file
sudo -u tomcat cp /tmp/bella.war /opt/tomcat/current/wepapps

# Unzip local WAR file
sudo -u tomcat unzip /tmp/bella.war /opt/tomcat/current/wepapps/bella

# Upload using cURL
curl --user robot:robot \
     --upload-file demo/bella.war \      
     "http://localhost:8080/manager/text/deploy?path=/bella&update=true"
```

--

## Manager Application

Manager App ist eine Webanwendung, die mit Tomcat ausgeliefert wird.
- Operation für Webapps:
  - Deploy
  - Remove
  - Reload
  - Clear Sessions
- Rollen
  - manager-gui
  - manager-script
  - manager-jmx
  - manager-status

--

## Context und Context Descriptor

- **Context** bezeichnet in Tomcat eine Webapp (Webanwendung).
- **Context Descriptor** ist eine XML-Datei, die Konfigurationen wie Ressourcen und Session-Management enthält.
- Früher in **server.xml** gespeichert, wird dies heute nicht mehr empfohlen.
<br>
<br>

### Speicherorte:
<br>

2. `$CATALINA_BASE/conf/[engine]/[host]/[webapp].xml` -> ausserhalb der Webapp
2. `$CATALINA_BASE/webapps/[webapp]/META-INF/context.xml` -> mit Webapp geliefert

<br>
<br>

- Fehlt ein Context Descriptor, nutzt Tomcat Standardwerte.
- Wird ein Context Descriptor erstellt oder aktualisiert, so wird die Webapp neu geladen.
- Defaults: `autoDeploy=true`, `deployOnStartup=true`, `unpackWARs=true`


--

## Context Descriptor Beispiel
<br>


```xml

<Context path="/bella" docBase="/tmp/bella">
  <!-- Other configurations -->
</Context>


```

<br>


- **`path`**: Context Pfad
- **`docBase`**: Ort der webapp (Verzeichnis mit entpacktem WAR-Archiv)
- Diese Attribute entfallen wenn der Descriptor im `META-INF`-Ordner der Webapp liegt.
<br>
<br>

> Dokumentation:
> https://tomcat.apache.org/tomcat-9.0-doc/config/context.html#Context_Parameters


--

## Security Überlegungen

- Dynamisches Deployment ist ein Sicherheitsrisiko:
  - Erlaubt Angreifern, die Kontrolle über das Filesystem erlangen, das Ändern, Neustarten oder Deployment von Webanwendungen.
  - Erschwert die Kontrolle darüber, welche Version einer Anwendung tatsächlich installiert ist.
  - Deployment beim Starten oder während des Betriebs beinflusst die Startzeit bzw. Performance.
  - Macht im Kontext von "Docker" keinen Sinn.
- Empfehlung:
  - Verwende nur statisches Deployment: `autoDeploy=false`, `deployOnStartup=false`
  - Schränke den Zugriff auf den Manager ein oder **entferne** ihn.
  - Verwende statische Context Descriptoren unter `/conf` oder entpacke WAR-Files manuell unter `/webapps` vor dem Start.


> Dokumentation:
> https://tomcat.apache.org/tomcat-9.0-doc/security-howto.html


--

## JSP Precompilation

- JSP-Dateien werden zur Laufzeit "beim ersten" Aufruf kompiliert. Bei Anwendungen mit sehr vielen JSPs kann das die Performance des Server beeinträchtigen.
- Tomcat bietet ein Tool zum Precompilieren von JSPs: Das Client Deployer Package (separate Distribution, basiert auf ANT)

> Dokumentation:
> https://tomcat.apache.org/tomcat-9.0-doc/deployer-howto.html#Deploying_using_the_Client_Deployer_Package

--
