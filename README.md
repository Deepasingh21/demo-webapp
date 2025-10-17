# demo-webapp
Minimal Maven Java web application (WAR) scaffold.

Structure created:
- src/main/java/com/example/demo/HelloServlet.java
- src/main/webapp/WEB-INF/web.xml
- src/main/webapp/index.jsp
- src/test/java/com/example/demo/HelloServletTest.java
- pom.xml
- Jenkinsfile

Build with: `mvn clean package`
Deploy the produced WAR in `target/demo-webapp-1.0-SNAPSHOT.war` to a servlet container (Tomcat / Jetty).
"# demo-webapp" 
