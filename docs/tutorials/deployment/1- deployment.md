# Web Applikationen deployen

In diesem Tutorial lernen wir verschiedene Wege zum Deployment einer Web Application in Tomcat kennen. Wir werden die folgenden Deployment-Methoden behandeln:

| Format      | Methode              | Beschreibung                                                 |
|-------------|----------------------|--------------------------------------------------------------|
| WAR         | Manager App          | Ein WAR-File über die Manager Webapp hochladen  und deployen |
| Verzeichnis | Manager App          | Ein Server-Verzeichnis deployen                              |
| WAR         | Manager App          | Ein WAR-File über ein Server-Verzeichnis deployen            |
| WAR         | Manuell              | WAR in den webapps-Ordner entpacken                          |
| WAR         | Manuell              | WAR in den webapp-Ordner kopieren                            |
| WAR         | CURL                 | WAR per CURL hochladen                                       |
| Verzeichnis | Context Descriptor   | Ein Context Descriptor File anlegen.                         |

> Dokumentation:
> https://tomcat.apache.org/tomcat-9.0-doc/deployer-howto.html#Deployment_on_Tomcat_startup
> https://tomcat.apache.org/tomcat-9.0-doc/manager-howto.html

## Vorraussetzungen
1. Stelle sicher das der Tomcat über Port `8080` auf `localhost` erreichbar ist. Starte falls erforderlich ein Port-Forwarding.
2. Lade die WAR-Datei [bella.war](..%2Ffiles%2Fbella.war) auf deinen Desktop.
3. Stelle sicher das in der tomcat-users.xml ein User mit der Rolle `manager-gui` existiert.
4. Öffne ein Konsolen-Fenster und beobachte das `catalina.out` Logfile mittels `tail -f logs/catalina.out`.
5. Beachte das alle Shell-Befehle mit dem User `tomcat` durchgeführt werden. Öffnen dazu eine Shell: `sudo -u tomcat /bin/bash`

## Schritt 1: Manager Webapp - WAR-File hochladen und deployen
>Das einfachste und am weitesten verbreitete Verfahren zum Deployment einer Web Application ist das Hochladen eines WAR-Files über die Manager Webapp. Die Manager Webapp ist eine Webanwendung, die mit Tomcat ausgeliefert wird und die es Administratoren ermöglicht, Anwendungen zu verwalten. Die Manager Webapp ist standardmäßig unter `http://localhost:8080/manager/html` erreichbar.

1. Melde dich an der Manager Webapp an: http://localhost:8080/manager/html
2. Unter "Installieren: Lokale WAR Datei zur Installation hochladen" wähle die Datei `bella.war` aus und klicke auf `Installieren`.
4. Beobachte wie im `catallina.out` Logfile die Deployment-Informationen erscheinen.'
5. Aktualisiere den Webbrowser. Die Liste der Anwendungen zeigt in der Spalte Kontext-Pfad [/bella](http://localhost:8080/bella/). Klick auf den Link um die Anwendung zu öffnen.
6. Abschliessend wechsel zurück in den Manager und Entferne die Anwendung über die Schaltfläche "Entfernen".

## Schritt 2: Serverseitige Shell - WAR-File aus /tmp-Ordner kopieren
>WAR-Files können auch auf dem Ziel-Server bereitgestellt werden, z.B. per SCP und liegen dort zunächst in einem Staging-Ordner. Per Copy-Befehl kann das WAR-File dann in den `webapps`-Ordner kopiert werden. Dieser Vorgang kann Alternativ auch über die Manager Webapp durchgeführt werden.

1. Kopieren das WAR-File `bella.war`per Secure Copy in den `/tmp`-Ordner des Servers: `scp -P 2222  "C:\Users\student\Desktop\bella.war" training@localhost:/tmp`
2. Auf dem Server kopiere das WAR-File in den `webapps`-Ordner: `cp /tmp/bella.war /opt/tomcat/current/webapps`
3. Bebobachte das `catallina.out` Logfile und beobachte wie die Anwendung deployt wird. Aktualisiere die Manager Webapp und beobachte wie die Anwendung erscheint.
4. Öffne die Anwendung im Browser und überprüfe ob sie funktioniert.
5. Entferne die Anwendung über die Manager Webapp.

## Schritt 3: Serverseitige Shell - WAR-File aus /tmp-Ordner entpacken
>Ähnlich wie zuvor wird das WAR-File aus dem /tmp-Ordner direkt in den webapps-Ordner entpackt. Der Tomcat erkennt die geänderten Dateien und deployed die Webapp. Dieses Verfahrem hat jedoch den Nachteil, das Dateien nicht gelöscht werden, die in der neuen Version nicht mehr benötigt werden.

1. Das WAR-File, das zuvor in den /tmp-Ordner kopiert wurde, wird nun per Unzip direkt entpackt. Beachte das das Archiv in einen Ordner mit dem Namen `bella` entpackt wird: `unzip /tmp/bella.war -d /opt/tomcat/current/webapps/bella`
3. Bebobachte das `catallina.out` Logfile und beobachte wie die Anwendung deployt wird. Aktualisiere die Manager Webapp und beobachte wie die Anwendung erscheint.
4. Öffne die Anwendung im Browser und überprüfe ob sie funktioniert.
5. Entferne die Anwendung über die Manager Webapp.

## Schritt 4: Manager Webapp: Server-seitiges WAR-File aus /tmp-Ordner deployen
>Ähnlich wie zuvor wird das WAR-File aus dem /tmp-Ordner über ein Kommando in der Webapp deployed. Dieses Verfahren ist sowohl für ein WAR-File und ebenso für ein bereits entpackets Verzeichnis möglich.

1. Öffne die Manager Webapp
2. Unter "Verzeichnis oder WAR Datei auf Server installieren" gibt in das Feld "WAR oder Verzeichnis URL" folgenden Pfad ein: `file:/tmp/bella.war' und klicke auf `Installieren`.
3. Bebobachte das `catallina.out` Logfile und beobachte wie die Anwendung deployt wird. Aktualisiere die Manager Webapp und beobachte wie die Anwendung erscheint.
4. Öffne die Anwendung im Browser und überprüfe ob sie funktioniert.
5. Entferne die Anwendung über die Manager Webapp.

## Schritt 4: Deployment per CURL
>Alle Methoden, die per Manager Webapp möglich sind, können auch per HTTP POST Request durchgeführt werden. Dazu gehören das deployen, entfernen oder neustarten einer App. Dies ist besonders nützlich, wenn das Deployment automatisiert werden soll. Als Tools kann z.B. cURL benutzt werden.

1. Stelle sicher, das in der tomcat-users.xml ein User mit der Rolle `manager-script` existiert. In der Beispielkonfiguration ist dies der User mit dem Namen `robot`.
2. Zum Hochladen und deployen eines WAR-Files benutze folgenden cURL-Befehl:
```bash
curl --user robot:robot \                                                            
     --upload-file C:\Users\student\Desktop\bella.war" \
     "http://localhost:8080/manager/text/deploy?path=/bella&update=true"
```
3. Bebobachte das `catallina.out` Logfile und beobachte wie die Anwendung deployt wird. Aktualisiere die Manager Webapp und beobachte wie die Anwendung erscheint.
4. Öffne die Anwendung im Browser und überprüfe ob sie funktioniert.

## Schritt 5: Deployment über Context Descriptor
>Ein Context Descriptor ist eine XML-Datei, die Konfigurationen wie Ressourcen und Session-Management enthält. Früher in **server.xml** gespeichert, wird dies heute nicht mehr empfohlen. Context Descriptors können in zwei Ordnern abgelegt werden: `$CATALINA_BASE/conf/[engine]/[host]/[webapp].xml` oder `$CATALINA_BASE/webapps/[webapp]/META-INF/context.xml`. Fehlt ein Context Descriptor, nutzt Tomcat Standardwerte. Wird ein Content Descriptor erstellt oder aktualisiert, so wird die Webapp neu geladen.

1. Entpacke die Datei `/tmp/bella.war` in das Verzeichnis `/tmp/my-bella`
2. Erstelle eine Datei `bella.xml` im Verzeichnis `/opt/tomcat/current/conf/Catalina/localhost/bella.xml` mit folgendem Inhalt:
```xml
<Context path="/bella" docBase="/tmp/my-bella">
  <!-- Other configurations -->
</Context>
```
4. Bebobachte das `catallina.out` Logfile und beobachte wie die Anwendung deployt wird. Aktualisiere die Manager Webapp und beobachte wie die Anwendung erscheint.

## Schritt 7: Reload der Webapp
>Eine Webapp kann auch ohne ein erneutes Deployment neu geladen werden. Dies ist besonders nützlich, wenn z.B. nur eine Datei gepatched wurde.

1. Füge einen Kommentar in die `web.xml` ein: `echo "<!-- Patched -->" >> /opt/tomcat/current/webapps/bella/WEB-INF/web.xml`
2. Bebobachte das `catallina.out` Logfile und beobachte wie die Anwendung neu geladen wird.
3. Eine Webapp kann auch über die Manager Webapp oder per cURL neu geladen werden.

## Schritt 8: Deployment mit Versionierung
Paralleles Deployment ermöglicht das Deployment von verschiedenen Versionen einer Webapp im gleichen Context-Pfad.
Über die User-Session wird gesteuert welcher Nutzer welcher App "zugeteilt" wird.
Somit bleiben bestehende User auf der "alten" Webapp und die neue Webapp kann getestet werden.
Ist der Test erfolgreich, kann die alte Webapp entfernt werden. Oder anderfalls wird die neue Webapp wieder gelöscht.
Die Versionsnummern sind "Text-Strings" und alphabetisch aufsteigend sortiert.

> Dokumentation:
> https://tomcat.apache.org/tomcat-9.0-doc/config/context.html#Parallel_deployment

1. Öffne ein Browser-Fenster im Privatmodus und öffne die Bella-Webapp: http://localhost:8080/bella. Hier wird die "alte" Version angezeigt.
2. Lade das WAR-File [bella-versiontest.war](..%2Ffiles%2Fbella-versiontest.war) auf den Desktop und deploye jetzt eine "neue" Version mit der Nummer "2":

```bash
curl --user robot:robot \                                                            
     --upload-file "bella-versiontest.war" \
     "http://localhost:8080/manager/text/deploy?path=/bella&update=true&version=2"
```


2. Überprüfe die Versionierung in der Manager Webapp. Die Webapp ist jetzt zweimal vorhanden.
3. Öffne einen zweiten Browser im Privat-Modus und öffne erneut http://localhost:8080/bella. Hier wird jetzt due "neue" Version angezeigt mit einem Hinweis.
4. Aktuaisiere die Seite in der "alten" Webapp. Hier wird weiterhin die Seite ohne Hinweis angezeigt,
5. Lösche jetzt die alte Webapp. Nach einer erneuten Aktualisierung wird auch im ersten Browser die "neue" Version angezeigt.