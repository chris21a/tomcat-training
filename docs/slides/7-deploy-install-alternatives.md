# Alternative Deployments und Installation

--

## Alternative Deployments

- [Ansible Playbook](https://github.com/ansible/ansible-examples/blob/master/tomcat-standalone/group_vars/tomcat-servers) (keine aktuellen Playbooks)
- Chef Cookbook
- [Docker]( https://hub.docker.com/_/tomcat)
- Kubernetes Helm Chart
- Kubernetes Operator
- Cloud-Service (AWS Bean Stalk, Azure App Service, Google App Engine)


--

## Tomcat on Docker
- Official Image: https://hub.docker.com/_/tomcat
- Viele Versionen und Java-Varianten verfügbar.
- Gehärtete und reduzierte Konfiguration: u.a. keine Default-Webapps installiert.


```bash
docker run -it --rm -p 8888:8080 tomcat:9.0
```

--

## Deploy Webapp using Docker

### Run Tomcat with external WAR-File:
```bash
docker run -d -p 8080:8080 --name my-tomcat \
  -v /path/to/your/app.war:/usr/local/tomcat/webapps/app.war \
  tomcat:latest
```
<br>
<br>

### Docker Image mit WAR-File erzeugen:

Dockerfile:
```bash
# Use the official Tomcat image as a base
FROM tomcat:9

# Copy your WAR file to the webapps directory inside the container
COPY /path/to/your/app.war /usr/local/tomcat/webapps/

# Expose the default Tomcat port
EXPOSE 8080
````
<br>
<br>
Docker Image erzeugen:

```bash
docker build -t my-tomcat-app .
docker run -d -p 8080:8080 --name my-tomcat-app my-tomcat-app
```

--

Default Environment:
```bash
CATALINA_BASE:   /usr/local/tomcat
CATALINA_HOME:   /usr/local/tomcat
CATALINA_TMPDIR: /usr/local/tomcat/temp
JRE_HOME:        /usr
CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
```

