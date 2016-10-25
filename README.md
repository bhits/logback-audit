# Logback Audit Server

The Consent2Share (C2S) application uses [Logback Audit](http://audit.qos.ch/) as a centralized audit repository in backend services, particularly the in Access Control Service components. Logback Audit can be configured to use relational databases for persistence.

C2S currently uses a fork of the [Logback Audit project](https://github.com/qos-ch/logback-audit). This fork is fundamentally the same as the  original Logback Audit implementation. However, it has some dependency version updates and column size modifications in the database tables. It also includes an SQL script for database creation and a generated *Logback Audit Server* project that can be built and deployed on an application server such as [Apache Tomcat](http://tomcat.apache.org/).

## Build

### Prerequisites

+ [Oracle Java JDK 8 with Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
+ [Docker Engine](https://docs.docker.com/engine/installation/) (for building a Docker image from the project)

### Commands

This is a Maven project and requires [Apache Maven](https://maven.apache.org/) 3.3.3 or greater to build it. It is recommended to use the *Maven Wrapper* scripts provided with this project. *Maven Wrapper* requires internet connection to download Maven and project dependencies for the very first build.

To build the project, navigate to the folder that contains the [**parent** `pom.xml` file](pom.xml) using terminal/command line.

+ To build a JAR:
    + For Windows, run `mvnw.cmd clean install & cd audit-server-generator\logback-audit-server & ..\..\mvnw.cmd clean install & cd ..\..`
    + For *nix systems, run `mvnw clean install; cd audit-server-generator/logback-audit-server/; ../../mvnw clean install; cd ../..`
+ To build a Docker Image (this will create an image with `bhits/logback-audit-server:latest` tag):
    + For Windows, run `mvnw.cmd clean install & cd audit-server-generator\logback-audit-server & ..\..\mvnw.cmd clean package docker:build & cd ..\..`
    + For *nix systems, run `mvnw clean install; cd audit-server-generator/logback-audit-server/; ../../mvnw clean package docker:build; cd ../..`

*NOTE: The `logback-audit-server` provided in this repository depends on the forked versions of Logback artifacts. Therefore, the entire project is required to be built as given in the commands above in order to build the deployable `logback-audit-server` project.*

## Run

### Prerequisites

This API uses *[MySQL](https://www.mysql.com/)* for persistence. It requires having a database user account with Object Rights to a schema with the default name `audit`. *Please see [Configure](#configure) section for details about configuring the data source.*

Currently, the Logback Audit Server does not support a database migration process, so the schema must be created manually. An [SQL file](audit-db/audit_tables-2014-04-07T102006.sql) is provided with this project to create the schema.

This API requires a separate application server to run it. [Apache Tomcat 8](http://tomcat.apache.org/) is the recommended application server to run this API. This API listens on a port that is separate than the application server port to communicate with other audit clients. Please see the [Configure](#configure) section for details of configuring the port number to listen to.

### Deployment

For easy deployment:

1. Find the `war` file located in `audit-server-generator/logback-audit-server/target` folder after building the project
2. Copy the `war` file to Tomcat's `webapps` folder
3. Configure Tomcat for *Logback Audit Server* properties *(See [Configure](#configure) section)*
4. Start up Tomcat

Please refer to [Tomcat Web Application Deployment](http://tomcat.apache.org/tomcat-8.0-doc/deployer-howto.html) documentation for more details about Tomcat deployment.

## Configure

This API depends on certain environment variables to be available in Tomcat. Please navigate to `$TOMCAT_HOME/conf/catalina.properties` and add the following variables:

+ `audit.datasource.url`: The URL for the database connection *(Example: `jdbc:mysql://localhost:3306/audit?autoReconnect=true`)*
+ `audit.datasource.username`: The username for the database connection *(Example: `root`)*
+ `audit.datasource.password`: The password for the database connection *(Example: `admin`)*
+ `audit.listen.port`: The port number that the audit server will listen to and communicate with audit clients. This port number is **NOT** the same as the application server port number. *(Example: `9630`)*
+ `C2S_PROPS`: This should be the location of root directory for externalized configuration. If `C2S_PROPS=/c2s-config`, the Logback Audit Server will try to load:
	+ `/c2s-config/logback-audit/config-template/logback-audit-config-logback_included.xml`: External [logback](http://logback.qos.ch/) file that will be included into the application logback configuration. This file can be used to configure logging details including where the log files will be stored and logging level. Please see the [sample included logback file](config-template/logback-audit-config-logback_included.xml).

+ `AUTO_SCAN`: This variable is used to configure [logback auto scan](http://logback.qos.ch/manual/configuration.html#autoScan) feature, so the expected value for this property is `true` or `false`. If `AUTO_SCAN=true`, logback will scan for changes in the included external configuration file and reconfigure itself when it detects a change.
+ `SCAN_PERIOD`: This variable is used to configure [logback auto scan period](http://logback.qos.ch/manual/configuration.html#autoScan) configuration. If `SCAN_PERIOD=30 seconds`, logback will scan the external file for changes for every 30 seconds.

### Provide Environment Variables While Running as a Docker Container

+ `docker run -d --link=audit-service-db.c2s.com -e "CATALINA_OPTS=-Daudit.datasource.url=jdbc:mysql://audit-service-db.c2s.com:3306/audit?autoReconnect=true -Daudit.datasource.username=root -Daudit.datasource.password=admin -Daudit.listen.port=9630 -DC2S_PROPS=/java/C2S_PROPS -DAUTO_SCAN=true -DSCAN_PERIOD='60 seconds'" -v "/path/to/config/root/on/dockerhost:/java/C2S_PROPS" bhits/logback-audit-server:latest`
+ In a `docker-compose.yml`, this can be provided as:

```yml
version: '2'
services:
...
  logback-audit-server.c2s.com:
    image: "bhits/logback-audit-server:latest"
    environment:
      CATALINA_OPTS: "-Daudit.datasource.url=jdbc:mysql://audit-service-db.c2s.com:3306/audit?autoReconnect=true -Daudit.datasource.username=root -Daudit.datasource.password=admin -Daudit.listen.port=9630 -DC2S_PROPS=/java/C2S_PROPS -DAUTO_SCAN=true -DSCAN_PERIOD='60 seconds'"
    volumes:
      - /path/to/config/root/on/dockerhost:/java/C2S_PROPS
...
```

[//]: # (## API Documentation)

[//]: # (## Notes)

[//]: # (## Contribute)

## Contact

If you have any questions, comments, or concerns please see [Consent2Share](https://bhits.github.io/consent2share/) project site.

## Report Issues

Please use [BHITS Logback Audit GitHub Issues](https://github.com/bhits/logback-audit/issues) page to report the issues related to Consent2Share modifications and [Logback Audit GitHub Issues](https://github.com/qos-ch/logback-audit/issues) page to report the issues related to the core Logback Audit framework.

[//]: # (License)

## Changes Made by the Consent2Share Development Team

+ Prepared this `README.md` file as a general technical documentation.
+ Released an unofficial version `0.6.1` containing Consent2Share modifications and moved the master branch to the next development version `0.6.2-SNAPSHOT`.
+ Updated `.gitignore` file and created a `.gitattributes` file.
+ Modified the column sizes in hibernate mappings and adjusted the existing unit test accordingly.
+ Generated a `logback-audit-server` project using `audit-server-generator` that supports:
	+ Externalized hibernate configuration using environment variables,
	+ JDBC connection and statement pooling using [`c3p0`](http://www.mchange.com/projects/c3p0/) library,
	+ Externalized logging configuration with an included `logback.xml` configuration file,
	+ Externalized port number configuration that the audit server listens to,
	+ Upgraded dependency versions for `logback` and `c3p0`,
	+ `maven-compiler-plugin` configuration with target Java version as 1.8.
+ Added additional code in `ServletContextListener.contextDestroyed` hook to wait for `c3p0 Resource Destroyer` thread to finish and also stop `LoggerContext` to prevent the application hanging on shutdown when there is an issue with the logger.
+ Added *Maven Wrapper* support.
+ Added a SQL Script for the `audit` schema.