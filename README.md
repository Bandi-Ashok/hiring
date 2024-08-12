FROM alpine:3.17
LABEL AUTHOR="Java Home"
RUN apk add openjdk17-jre

WORKDIR /opt

ADD https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.80/bin/apache-tomcat-9.0.80.tar.gz .
RUN tar xf apache-tomcat-9.0.80.tar.gz
RUN rm -rf apache-tomcat-9.0.80.tar.gz
RUN mv apache-tomcat-9.0.80 tomcat9

# Update Tomcat to listen on port 9090
RUN sed -i 's/8080/9090/g' /opt/tomcat9/conf/server.xml

COPY target/hiring.war /opt/tomcat9/webapps

# Expose port 9090
EXPOSE 9090

CMD ["/opt/tomcat9/bin/catalina.sh", "run"]
