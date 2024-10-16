systemctl start tomcat-m@1 tomcat-m@2 tomcat-m@3 tomcat-m@4

------------------------

lrwxrwxrwx  1 tomcat tomcat    32 Oct 14 10:12 current -> /opt/tomcat/apache-tomcat-9.0.96
drwxr-xr-x 10 tomcat tomcat  4096 Oct 15 12:02 apache-tomcat-9.0.96
drwxr-xr-x  6 tomcat tomcat  4096 Oct 15 12:35 instanz2
drwxr-xr-x  6 tomcat tomcat  4096 Oct 15 12:35 instanz3
drwxr-x---  8 tomcat tomcat  4096 Oct 15 12:35 .
drwxr-xr-x  6 tomcat tomcat  4096 Oct 15 12:35 instanz4
drwxr-xr-x  4 tomcat tomcat  4096 Oct 15 13:26 binary
drwxr-xr-x  5 tomcat tomcat  4096 Oct 15 14:23 instanz1

------------------------
/etc/systemd/system/tomcat-m\@.service:
[Unit]
Description=Apache Tomcat - Instanz %i
After=network.target

[Service]
Type=forking

# Berechnungen und Start des Tomcat-Servers in einer Shell-Umgebung
ExecStart=/opt/tomcat/binary/bin/startup.sh
ExecStop=/opt/tomcat/binary/bin/shutdown.sh

Environment="INSTANZ=%i"
User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target

--------------------
/opt/tomcat/binary/bin/setenv.sh:
#!/bin/bash
echo $INSTANZ

SHUTDOWN_PORT=$((9105 + $INSTANZ))
HTTP_PORT=$((8080 + $INSTANZ))
HTTP_PORT_TWO=$((8765 + $INSTANZ))
AJP_PORT=$((8009 + $INSTANZ))

JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
CATALINA_PID=/opt/tomcat/current/temp/tomcat-instanz-$INSTANZ.pid
CATALINA_HOME=/opt/tomcat/binary
CATALINA_BASE="/opt/tomcat/instanz$INSTANZ"
CATALINA_OPTS="-Xms512M -Xmx1024M -server -XX:+UseParallelGC"
JAVA_OPTS=-Djava.awt.headless=true


CATALINA_OPTS="$CATALINA_OPTS -Dorg.apache.tomcat.util.digester.PROPERTY_SOURCE=org.apache.tomcat.util.digester.EnvironmentPropertySource"
CATALINA_OPTS="$CATALINA_OPTS -Dhttp.port=$HTTP_PORT"
CATALINA_OPTS="$CATALINA_OPTS -Dshutdown.port=$SHUTDOWN_PORT"
CATALINA_OPTS="$CATALINA_OPTS -Dhttp_two.port=$HTTP_PORT_TWO"
CATALINA_OPTS="$CATALINA_OPTS -Dajp.port=$AJP_PORT"