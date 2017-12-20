# MicroProfile Tutorial (Under Construction)

This tutorial demonstrates the use of MicroProfile technologies for implementing a set of Microservices.  The tutorial is made up of a number of sub modules each of which demonstrates different MicroProfile technologies. Each module builds on the earlier modules, but can be taken independently as the starting point of a module is the end result of the previous module. This means you can choose to go through all the modules in sequence, or if you just want to learn about a specific technology, you can choose to just do the module covering that capability.

Each Module is held in a separate Git branch in this repository.  The README.md in each branch contains the instructions for the module and so changes each time you switch branches.

## The Modules

### Module 1

This module demonstrates the use of JAX-RS, CDI and JSON-P to implement three Microservices.  The first is a gateway service that provides a single point for an Angular front-end to call.  The gateway service is responsible for routing requests through to two back-end services that provide the functionality for the Web front-end.

### Module 2

This module demonstrates the use of MicroProfile Fault Tolerance.  Fault Tolerance enables the applications to function even when on of the services is unavailable. 

## !!!The following modules are still work in progress!!!

### Module 3

This module demonstrates how to secure a microservice using MicroProfile JWT.  The JWT support enables service access to be controlled based on the role of an authenticated user.

### Module 4

This module demonstrates how to externalize microservice configuration, such as http ports, using MicroProfile Config.  Externalizing configuration means a microservice can be deployed to different environments without the needs for a re-build and full re-testing.

### Module 5

This module demonstrates the use of MicroProfile Health and Metrics.  MicroProfile Health provides a simple status as to whether a microservice is "UP" or "DOWN".  MicroProfile Metrics provides more detailed metrics about a service, such as CPU load, JVM usage.

## Tutorial Theme

We have decided to go with a space theme for this tutorial to keep in line with the Open Liberty theme.

Think of the front-end web application as the on board computer on the space ship you are currently flying.

The Gateway is one of the galaxies communication carriers that enable your ships computer to talk to other devices in the galaxy.

Microservice A will act as a space ship and provide current information via rest api calls such as the operating system the ship is running on and it's architecture etc.

Microservice B will act as a space stations on board computer that will provide current information about the station such as currently docked ships, personal, location and information about the technology used on the station.

The fallback microservice B will provide older information about the space station incase the stations on board computer fails so that a connection can still be made to the station rather than space ships not being able to communicate with the station at all.

### Prerequisites

1. Maven (https://maven.apache.org/install.html) - for building, testing and running our microservices
1. npm (https://www.npmjs.com/get-npm) - for installing Angular and the Angular CLI
1. Angular (https://www.npmjs.com/package/angular) - for building and running our web application
1. Angular CLI (https://cli.angular.io/)
1. Git CLI (https://git-scm.com/downloads) - for downloading the source files for this tutorial
1. A Java IDE (Eclipse, Visual Studio Code, NetBeans etc) - for coding our tutorial
1. Connection to the Internet - to pull down the required dependencies

### Use of Github

The Github approach of using a single repository with many branches is not a microservices best practice, it is done to aid structuring the tutorial.  With microservice deployments, a typical approach is to use one or more repositories per microservice.  This helps maintain independence between the microservices in terms of personnel, code and life-cycle.  

## Information About The Files We Have Provided

We have provided some file for you to make this tutorial easier to consume that I will talk about here.
Firstly we have provided you with the basic directory structure for all the microservices.
Then we have provided you with a pom.xml file that is used by maven to build your microservices and download all the required dependencies. In this file you can set what the application is named `<app.name>LibertyMicroServiceOne-1.0</app.name>`, the ports assigned to Liberty 

```XML
<testServerHttpPort>9090</testServerHttpPort> <testServerHttpsPort>9444</testServerHttpsPort>
```

the dependancies you require like microProfile 1.2

```XML
<dependency>
    <groupId>org.eclipse.microprofile</groupId>
    <artifactId>microprofile</artifactId>
    <version>1.2</version>
    <scope>provided</scope>
    <type>pom</type>
</dependency>
```

and the application server you are using so in our case open Liberty

```XML
<plugin>
    <groupId>net.wasdev.wlp.maven.plugins</groupId>
    <artifactId>liberty-maven-plugin</artifactId>
    <version>2.1</version>
        <extensions>true</extensions>
            <configuration>
                <assemblyArtifact>
                    <groupId>io.openliberty</groupId>
                    <artifactId>openliberty-runtime</artifactId>
                    <version>17.0.0.3</version>
                    <type>zip</type>
                </assemblyArtifact>
                    <configFile>${basedir}/src/main/liberty/config/server.xml</configFile>
                    <serverEnv>${basedir}/src/main/liberty/config/server.env</serverEnv>
                    <jvmOptionsFile>${basedir}/src/main/liberty/config/jvm.options</jvmOptionsFile>
                    <packageFile>${package.file}</packageFile>
                    <include>${packaging.type}</include>
                    <bootstrapProperties>
                        <default.http.port>${testServerHttpPort}</default.http.port>
                        <default.https.port>${testServerHttpsPort}</default.https.port>
                    </bootstrapProperties>
            </configuration>
                <executions>
                    <execution>
                        <id>install-liberty</id>
                        <phase>prepare-package</phase>
                        <goals>
                            <goal>install-server</goal>
                        </goals>
                    </execution>
                <execution>
                    <id>package-app</id>
                    <phase>package</phase>
                    <goals>
                        <goal>package-server</goal>
                    </goals>
                </execution>
                <execution>
                    <id>test-start-server</id>
                    <phase>pre-integration-test</phase>
                    <goals>
                        <goal>test-start-server</goal>
                    </goals>
                </execution>
                <execution>
                    <id>test-stop-server</id>
                    <phase>post-integration-test</phase>
                        <goals>
                            <goal>test-stop-server</goal>
                        </goals>
                </execution>
            </executions>
        </plugin>
```

We have also provided you with a file named server.xml  that you will use to configure our liberty server. The server.xml files are located in all the microservice directories in this repo `<microservice-***>/src/main/liberty/config/`

```XML
<server description="Sample Liberty server">

  <featureManager>
      <feature>microprofile-1.2</feature>
  </featureManager>

  <httpEndpoint httpPort="9091" httpsPort="${default.https.port}"
      id="defaultHttpEndpoint" host="*" />
</server>
```

Above is the contents of the server.xml file. The section:
`<featureManager>` allows you to define what Liberty features your require in your server.
The `<httpEndpoint>` section
