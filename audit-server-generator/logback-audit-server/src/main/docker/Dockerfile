FROM tomcat:8-jre8
# Properties used in logback.xml
ENV AUTO_SCAN true
ENV SCAN_PERIOD 1 seconds
# Root folder for external configuration files like included logback.xml
ENV C2S_PROPS /java/C2S_PROPS
# Copy war to container, argument is defined in pom.xml
ARG finalName
COPY $finalName /usr/local/tomcat/webapps/