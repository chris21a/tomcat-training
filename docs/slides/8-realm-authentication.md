# Realms 
### Authentifizierung und Autorisierung


--

## Realms

- Authentifizierung und Autorisierung von Benutzern wird über Realms gesteuert.
- Realm kann Benutzerinformationen aus unterschiedlichen Quellen laden, z.B. aus einer Datenbank oder einem
  LDAP-Verzeichnis.
- Mit einem Real kann der Zugriff auf eine Webapp über eine Benutzerverwaltung geschützt werden
- Implementierungen: MemoryRealm (tomcat-users.xml), JDBCRealm, JNDIRealm (LDAP)

> Dokumentation: https://tomcat.apache.org/tomcat-9.0-doc/config/realm.html

```xml

<Realm className="org.apache.catalina.realm.JDBCRealm"
       driverName="com.mysql.cj.jdbc.Driver" connectionURL="jdbc:mysql://localhost:3306/tomcat_users"
       connectionName="root" connectionPassword="password" userTable="users" userNameCol="username"
       userCredCol="password" userRoleTable="user_roles" roleNameCol="rolename"/>

```


--

### Rollen und Benutzer

- MemoryRealm speichert Benutzer in der Datei `tomcat-users.xml`
- Rollen ermöglichen eine feingranulare Steuerung des Zugriffs.
- Benutzer können mehreren Rollen zugeordnet werden.

```xml
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<user username="admin" password="secret" roles="manager-gui,admin-gui"/>
<user username="developer" password="devpass" roles="manager-script"/>
```

--

#  Webapp
Beispiel für die Security Config in einer Webaoo.

```xml
<web-app>
    <!-- Define security constraint to protect /app -->
    <security-constraint>
        <web-resource-collection>
            <web-resource-name>Protected Area</web-resource-name>
            <url-pattern>/*</url-pattern>
        </web-resource-collection>
        <auth-constraint>
            <role-name>role1</role-name>
            <role-name>role2</role-name>
        </auth-constraint>
    </security-constraint>

    <!-- Define login configuration -->
    <login-config>
        <auth-method>BASIC</auth-method> <!-- or DIGEST, FORM, CLIENT-CERT -->
        <realm-name>UserDatabaseRealm</realm-name>
    </login-config>
    
    <!-- Define the security roles used in the app -->
    <security-role><role-name>role1</role-name></security-role>
    <security-role><role-name>role2</role-name></security-role>
</web-app>

```
<!-- .element: class="r-stretch" -->



--

# Context

- **Context**: Definiert Konfigurationseinstellungen für eine bestimmte Webanwendung.
- Jeder Kontext kann eigene Realms und Sicherheitskonfigurationen haben.
-  Context kann auch Roles definieren und das Zugriffsverhalten der Anwendung steuern.

```xml
<Context path="/myapp" docBase="myapp">
    <Realm className="org.apache.catalina.realm.MemoryRealm" />
    
    <!-- BasicAuthenticator wird automatisch gesetzt wenn web.xml BASIC Auth Method anfordert -->
    <Valve className="org.apache.catalina.authenticator.BasicAuthenticator" />
</Context>

```


--

# Zusammenfassung

- **Realms**: Dienen der Authentifizierung und Autorisierung.
- **Roles**: Definieren Benutzerrollen für den Zugriff.
- **Valves**: Bieten zusätzliche Sicherheits- und Überwachungsfunktionen.
- **Context**: Konfiguriert den Anwendungsbereich und kann Realms und Valves enthalten.