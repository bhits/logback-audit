
# Short Description

Logback Audit Server is responsible for storing audit events from audit clients.

# Full Description

# Supported Tags and Respective `Dockerfile` Links

[`0.6.3 (latest)`](https://github.com/bhits/logback-audit/releases/tag/v_0.6.3), [`0.6.2`](https://github.com/bhits/logback-audit/releases/tag/v_0.6.2), [`0.6.1`](https://github.com/bhits/logback-audit/releases/tag/v_0.6.1)

[`Current Dockerfile`](https://github.com/bhits/logback-audit/blob/master/audit-server-generator/logback-audit-server/src/main/docker/Dockerfile)

For more information about this image, the source code, and its history, please see the [GitHub repository](https://github.com/bhits/logback-audit).

# What is Logback Audit Server?

The Consent2Share application uses [Logback Audit](http://audit.qos.ch/) as a centralized audit repository in backend services, particularly in the Access Control Service components. Logback Audit can be configured to use relational databases for persistence.

Consent2Share currently uses a fork of the [Logback Audit project](https://github.com/qos-ch/logback-audit). This fork is fundamentally the same as the  original Logback Audit implementation. However, it has some dependency version updates and column size modifications in the database tables. It also includes an SQL script for database creation and a generated *Logback Audit Server* project that can be built and deployed on an application server such as [Apache Tomcat](http://tomcat.apache.org/).

This image provides the runtime environment for the generated *Logback Audit Server* application.

For more information and related downloads for Consent2Share, please visit [Consent2Share](https://bhits.github.io/consent2share/).

# How to Use This Image

## Start a Logback Audit Server Instance

Be sure to familiarize yourself with the repository's [README.md](https://github.com/bhits/logback-audit) file before starting the instance.

`docker run -d --name=logback-audit-server.c2s.com --link=audit-service-db.c2s.com -e "CATALINA_OPTS=-Daudit.datasource.url=jdbc:mysql://audit-service-db.c2s.com:3306/audit?autoReconnect=true -Daudit.datasource.username=root -Daudit.datasource.password=admin -Daudit.listen.port=9630 -DC2S_PROPS=/java/C2S_PROPS -DAUTO_SCAN=true -DSCAN_PERIOD='60 seconds'" -v "/path/to/config/root/on/dockerhost:/java/C2S_PROPS" bhits/logback-audit-server:latest`

*NOTE: In order for this API to fully function as a microservice in the Consent2Share application, it is required to setup the dependency microservices and support level infrastructure. Please refer to the [Consent2Share Deployment Guide](https://github.com/bhits/consent2share/releases/download/2.0.0/c2s-deployment-guide.pdf) for instructions to setup the Consent2Share infrastructure.*

## Configure

This API depends on certain environment variables to be available in Tomcat and a [logback-audit-config-logback_included.xml](https://github.com/bhits/logback-audit/blob/master/config-template/logback-audit-config-logback_included.xml) to be mounted in the container. Please see the following environment variables for configuration.

## Environment Variables

The `logback-audit-server` can be configured by providing the following environment variables.

### C2S_PROPS

This should be the location of the root directory for externalized configuration. The default value is `/java/C2S_PROPS`. The `logback-audit-server` will try to load `${C2S_PROPS}/logback-audit/config-template/logback-audit-config-logback_included.xml`.

This environment variable can be overriden by passing through CATALINA_OPTS. Make sure you put the configuration file under the new configured path.

`docker run -d --name=logback-audit-server.c2s.com --link=audit-service-db.c2s.com -e "CATALINA_OPTS=-DC2S_PROPS=/new/path -v "/path/to/config/root/on/dockerhost/logback-audit/config-template/logback-audit-config-logback_included.xml:/new/path/logback-audit/config-template/logback-audit-config-logback_included.xml" bhits/logback-audit-server:latest`

### AUTO_SCAN 

This variable is used to configure [logback auto scan](http://logback.qos.ch/manual/configuration.html#autoScan) feature, so the expected value for this property is `true` or `false`. If `AUTO_SCAN=true`, logback will scan for changes in the included external configuration file and reconfigure itself when it detects a change. The default value is `true`.

`docker run -d --name=logback-audit-server.c2s.com -e "CATALINA_OPTS=-DAUTO_SCAN=true" bhits/logback-audit-server:latest`

### SCAN_PERIOD 

This variable is used to configure [logback auto scan period](http://logback.qos.ch/manual/configuration.html#autoScan) configuration. If `SCAN_PERIOD=30 seconds`, logback will scan the external file for changes for every 30 seconds. The default value is `1 seconds`.

`docker run -d --name=logback-audit-server.c2s.com -e "CATALINA_OPTS=-DSCAN_PERIOD='30 seconds'" bhits/logback-audit-server:latest`

### audit.datasource.url

This is the URL for the database connection. There is no default value for this configuration, so it must be provided.

`docker run -d --name=logback-audit-server.c2s.com --link=audit-service-db.c2s.com -e "CATALINA_OPTS=-Daudit.datasource.url=jdbc:mysql://audit-service-db.c2s.com:3306/audit?autoReconnect=true" bhits/logback-audit-server:latest`

### audit.datasource.username

This is the username for the database connection. There is no default value for this configuration, so it must be provided.

`docker run -d --name=logback-audit-server.c2s.com -e "CATALINA_OPTS=-Daudit.datasource.username=root" bhits/logback-audit-server:latest`

### audit.datasource.password

This is the password for the database connection. There is no default value for this configuration, so it must be provided.

`docker run -d --name=logback-audit-server.c2s.com -e "CATALINA_OPTS=-Daudit.datasource.password=admin" bhits/logback-audit-server:latest`

### audit.listen.port

This is the port number that the audit server will listen to and communicate with audit clients. This port number is **NOT** the same as the application server port number. There is no default value for this configuration, so it must be provided. However, the default expected value in several audit clients is `9630`.

`docker run -d --name=logback-audit-server.c2s.com -e "CATALINA_OPTS=-Daudit.listen.port=9630" bhits/logback-audit-server:latest`


# Supported Docker Versions

This image is officially supported on Docker version 1.12.1.

Support for older versions (down to 1.6) is provided on a best-effort basis.

Please see the [Docker installation documentation](https://docs.docker.com/engine/installation/) for details on how to upgrade your Docker daemon.

# License

The Logback Audit license can be found at [http://audit.qos.ch/license.html](http://audit.qos.ch/license.html).

# User Feedback

## Documentation

Documentation for this image is stored in the [bhits/logback-audit](https://github.com/bhits/logback-audit) GitHub repository. Be sure to familiarize yourself with the repository's README.md file before attempting a pull request.

## Issues

If you have any problems/questions about this image or Consent2Share modifications on Logback Audit, please contact us through the [BHITS Logback Audit GitHub Issues](https://github.com/bhits/logback-audit/issues) page. For issues related to the core Logback Audit framework, please use [Logback Audit GitHub Issues](https://github.com/qos-ch/logback-audit/issues) page.