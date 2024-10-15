# Sessions


--

## Sessions in Tomcat

- Sessions werden von der Webapp genutzt um Sitzungsdaten wie Logins zu speichern
- Sessions nutzen zur eindeutigen Identifikation von CLients einen Cookie oder alternativ URL-Parameter 'JSESSIONID'. Alternativ auch SSL-Session.
- Parameter wie Sessions-Timeout, Verfahren und Sessions-Persistenz werden im Kontext der Webapp konfiguriert


--

## Grundlegende Session-Konfiguration in Tomcat

Sessions-Manager:
- **StandardManager**: Verwaltet Sessions im Speicher und persistiert Sessions für Restarts in einer Datei
- **PersistentManager**: Kann zusätzlich Sessions in einem Database Backend speichern.


```xml
<Context>
  <Manager className="org.apache.catalina.session.PersistentManager"
           saveOnRestart="true" maxActiveSessions="100">
           
    <Store className="org.apache.catalina.session.FileStore" directory="session_storage"/>
  </Manager>
</Context>
```