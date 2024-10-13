# Konfiguration der Benutzerdatenbank

# Schritt 1: Editieren der `tomcat-users.xml` Datei

- Erstelle einen User `training` mit dem Passwort `changeme` und der Rolle `manager-gui`
- Unkommentiere die Zeilen für die Rolle `tomcat` und die User `tomcat`, `both` und `role1`

```xml

<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">

  <user username="training" password="changeme" roles="manager-gui"/>

  <role rolename="tomcat"/>
  <role rolename="role1"/>
  <user username="tomcat" password="changeme" roles="tomcat"/>
  <user username="both" password="changeme" roles="tomcat,role1"/>
  <user username="role1" password="changeme" roles="role1"/>

</tomcat-users>


```

# Schritt 2: Neustart des Servers
- Änderungen an der tomcat-users.xml werden nicht zur Laufzeit neu geladen
- Starte den Serer neu, um die Änderungen zu übernehmen. Siehe "[Installation](../../slides/3-install.md): Service starten".

# Schritt 3: Testen der Konfiguration
- Überprüfe ob der neue User funktiert
- Öffne die Manager Webapp unter http://localhost:8080/manager/html und benutzt den neuen User.
- Falls es nicht funktioniert ünerprüfe das Logfile unter `/opt/tomcat/current/logs/catalina.out`

# Schritt 4: Test die Example App
- Öffne die URL http://localhost:8080/examples/jsp/security/protected/index.jsp
- Logge dich mit Kombinationen der User `both` und `training` ein
- Der User `training` sollte keinen Zugriff haben
