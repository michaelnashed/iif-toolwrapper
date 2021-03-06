Tool Wrapper v0.7 [![Build Status](https://secure.travis-ci.org/impactcentre/iif-toolwrapper.png?branch=master)](http://travis-ci.org/impactcentre/iif-toolwrapper)
-----------------
 
**About**

The Tool Wrapper is a Java based application for creating a web service wrapper
project for command line tools. Based on an XML tool description, the application
creates a maven project for the tool which includes web services and web service
operations allowing to execute a command line tool via SOAP, RESTless, and Java
API interfaces.

**Quick start**

You need at least the Java SE Development Kit (JDK) version >=1.6.0 and Apache
Maven >= 2.2.1. For testing an internal jetty server is used for deployment,
for productive deployment an Apache Tomcat application server version >=6.0.18
is recommended. Also make sure the JAVA_HOME environment variable is defined.

1) In the toolwrapper directory, run the maven package command:

	mvn package

2) If the build is successful (it should be), then run the toolwrapper jar you've just built

    java -jar ./target/toolwrapper-0.7.0-jar-with-dependencies.jar

This will generate an example project generated/simplecopy10. This service is
a an example wrapper project for the linux copy command "cp" or the COPY
command for Windows (use "CMD.exe /C COPY" instead of "cp" in the command
pattern defined in the tool specification default.xml in node toolspec/services/service/operations/operation/command if you are using windows).

Customized project properties or another tool specification can be used:

    java -jar toolwrapper.jar -p toolwrapper.properties -t default-toolspec.xml

where:

    -p (--props) specifies the file that contains the project configuration
    -t (--toolspec) Tool, service and service input/output data type defintions

3) Change to the directory where the sample project has been generated:

    cd ./generated/simplecopy10

and then deploy the example service to the built in Jetty service:

    mvn jetty:run

4) Accessing the Axis2 service:

You can find the start page of the web application at:

    http://localhost:8080/impactservices/impact-simplecopy10-service/

and the WSDL of the service at:

    http://localhost:8080/impactservices/impact-simplecopy10-service/SimpleCopy10.wsdl

Try the RESTless interface:

    http://localhost:8080/impactservices/impact-simplecopy10-service/services/SimpleCopy10/simpleCopy?inputUrl=http://localhost:8080/impactservices/impact-simplecopy10-service/test.txt

and - if everything is setup correctly - you would receive a response like:

```xml
    <ns:simpleCopyResponse xmlns:ns="http://impact-project.eu/iif/services">
       <ns:return>
           <tns:result xmlns:tns="http://impact-project.eu/iif/services">
               <tns:success>true</tns:success>
               <tns:returncode>0</tns:returncode>
               <tns:time>115</tns:time>
               <tns:log>========= PROCESSING REQUEST =========.
                Using service: IMPACTSimpleCopy10Service.
                Parameter processingUnit from services.xml: http://localhost:8080/impact/instances/tomcat1.
                URL of input file: http://localhost:8080/impactservices/impact-simplecopy10-service/test.txt.
                Input file created: /tmp/simplecopy10-tmp-store/fromurl7101255115685982047.txt.
                Pattern (before substitution): /bin/cp ${input} ${output}.
                Input substitution variable value: /tmp/simplecopy10-tmp-store/fromurl7101255115685982047.txt.
                Output substitution variable value: /tmp/simplecopy10-tmp-store/test.txt_IMPACTSimpleCopy10Service_outputFile_6391763547702641327.txt.
                Command (after substitution): /bin/cp /tmp/simplecopy10-tmp-store/fromurl7101255115685982047.txt /tmp/simplecopy10-tmp-store/test.txt_IMPACTSimpleCopy10Service_outputFile_6391763547702641327.txt.
                Assigned exit code: 0.
                Process finished successfully with code 0.
                Output file of size 21 has been created successfully.
                Public output file: src/main/webapp/test.txt_IMPACTSimpleCopy10Service_outputFile_6391763547702641327.txt.
                Output URL: http://localhost:8080/impact/tmp/test.txt_IMPACTSimpleCopy10Service_outputFile_6391763547702641327.txt.
                Process finished successfully after 115 milliseconds.
                Service request has been processed successfully.
               </tns:log>
               <tns:message>Service request has been processed successfully</tns:message>
               <tns:output>http://localhost:8080/impact/tmp/test.txt_IMPACTSimpleCopy10Service_outputFile_6391763547702641327.txt</tns:output>
           </tns:result>
       </ns:return>
    </ns:simpleCopyResponse>
```

The service simply has copied the file available at the URL  
   
    http://localhost:8080/impactservices/impact-simplecopy10-service/test.txt

to a temporary location and then made it available at a URL 

    http://localhost:8080/impact/tmp/test.txt_IMPACTSimpleCopy10Service_outputFile_6391763547702641327.txt.

   Note regarding jetty: Jetty is using the test text file available in the jetty webapp directory:
   toolwrapper/generated/simplecopy10/src/main/webapp/test.txt, also for storing temporary files, the default tool specificatioin
   has configured the src/main/webapp/ directory as temporary directory (see default-toolspec.xml - 
   /toolspec/deployments/deployment/dataexchange/accessdir).

5) Try out the SOAP web service with soapUI by creating a project using the WSDL of the service:

       http://localhost:8080/impactservices/impact-simplecopy10-service/SimpleCopy10.wsdl

by executing the soap request:

```xml
    <soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope" xmlns:ser="http://impact-project.eu/pc/services">
     <soap:Header/>
      <soap:Body>
          <ser:Request1>
             <ser:input>http://fue.onb.ac.at/scape/testdata/test.txt</ser:input>
          </ser:Request1>
       </soap:Body>
    </soap:Envelope>
```

You would then receive a response containing a reference to the copied result file, like:

```xml
    <soapenv:Envelope xmlns:soapenv="http://www.w3.org/2003/05/soap-envelope">
       <soapenv:Body>
          <ns:simpleCopyResponse xmlns:ns="http://impact-project.eu/pc/services">
             <ns:return>
                <tns:result xmlns:tns="http://impact-project.eu/pc/services">
                   <tns:success>true</tns:success>
                   <tns:returncode>0</tns:returncode>
                   <tns:time>22</tns:time>
                   <tns:log>========= PROCESSING REQUEST =========.
                    Using service: SimpleCopy10.
                    URL of input file: http://fue.onb.ac.at/scape/testdata/test.txt.
                    Input file created: /usr/local/java/apache-tomcat-6.0.29/temp/simplecopy10-tmp-store/fromurl2399759179719296814.txt.
                    Pattern (before substitution): cp $INFILE $OUTFILE.
                    OUTFILE substitution variable value: /usr/local/java/apache-tomcat-6.0.29/temp/simplecopy10-tmp-store/fromurl2399759179719296814.txtIMPACTSimpleCopy10Service_outputFile_8185195208001481281.txt.
                    INFILE substitution variable value: /usr/local/java/apache-tomcat-6.0.29/temp/simplecopy10-tmp-store/fromurl2399759179719296814.txt.
                    Command (after substitution): cp /usr/local/java/apache-tomcat-6.0.29/temp/simplecopy10-tmp-store/fromurl2399759179719296814.png /usr/local/java/apache-tomcat-6.0.29/temp/simplecopy10-tmp-store/fromurl2399759179719296814.pngIMPACTSimpleCopy10Service_outputFile_8185195208001481281.txt.
                    Assigned exit code: 0.
                    Process finished successfully with code 0.
                    Output file of size 24805 has been created successfully.
                    Public output file: /usr/local/java/apache-tomcat-6.0.29/webapps/ROOT/impact/tmp/fromurl2399759179719296814.pngIMPACTSimpleCopy10Service_outputFile_8185195208001481281.txt.
                    Output URL: http://localhost:8080/impact/tmp/fromurl2399759179719296814.pngIMPACTSimpleCopy10Service_outputFile_8185195208001481281.txt.
                    Service request has been processed successfully.</tns:log>
                   <tns:message>Service request has been processed successfully</tns:message>
                   <tns:output>http://localhost:8080/impact/tmp/fromurl2399759179719296814.pngIMPACTSimpleCopy10Service_outputFile_8185195208001481281.txt</tns:output>
                </tns:result>
             </ns:return>
          </ns:simpleCopyResponse>
       </soapenv:Body>
    </soapenv:Envelope>
```

**Create a new project**

1) Configure the project by creating configuration files:

   a) *toolwrapper.properties*  
Project configuration file defining the main project generation properties that will be used for all tools and services.

   b) *default.xml*  
Tool, service and service input/output data type definitions which must comply with the toolwrapper/src/main/resources/toolspec.xsd schema.

See the "examples" folder which contains more configuration files.

2) Run the project generator (default arguments):

    java -jar toolwrapper.jar

or indicating the configuration files to be used:

    java -jar toolwrapper.jar -p toolwrapper.properties -t default.xml

where:

    -p (--props) specifies the file that contains the project configuration
    -t (--toolspec) Tool, service and service input/output data type defintions

It is possible to define multiple services for each tool, for each service a separate maven project will be created. Each service can have multiple operations which correspond to specific commands. Some examples of tool configuration files are provided in the "examples" directory.

It is possible to assign multiple deployment environments to a service, different web application archives (war) will be created for deployment. Using the corresponding profile in the deployment, a specific deployment environment can be selected (e.g. mvn tomcat:deploy -P deployment1).

The project will be created in the "generated" folder, the name of the project folder being derived from the service name and the tool version.

3) Follow the README file of the generated project

**Project structure**

- examples  
Example tool configuration files

- generated  
The default directory where projects will be created (can be configured in the toolwrapper.properties file)

- tmpl  
Code templates used for code insertion.

- generated  
Folder where the generated SOAP web service wrapper projects will be stored.

- logs  
The log files will be stored in this folder

- src  
Java sources of the Tool Wrapper

- target  
Build files

- template  
Project template (substitution variables in the template files)

- toolwrapper.jar  
Java binary executable (execute "java -jar toolwrapper.jar")

- toolwrapper.properties  
Project configuration file

- default.xml  
Example for tool, service and service input/output data type defintions which is used by default and corresponds to the "copy" command.

- LICENSE  
Apache 2 license

- pom.xml  
Maven project configuration file

- README  
This file

**Requirements**

You need at least the Java SE Development Kit (JDK) version >=1.6.0 and Apache
Maven >= 2.2.1. Also make sure the JAVA_HOME environment variable is defined.

If you want to create a web service for an existing command line application
using this java based tool web service wrapper, the following software is
required:

- Underlying command line tool of this web service wrapper installed in a path
  without white spaces.
  Install the underlying command line tool of the tool web service wrapper
  on your system.
  IMPORTANT: THE UNDERLYING COMMAND LINE TOOL FOR THE SERVICE SHOULD BE
  INSTALLED IN A PATH WITHOUT WHITE SPACES. OTHERWISE A FULL PATH CALL OF THE
  COMMAND LINE TOOL WILL NOT WORK CORRECTLY.

- Java SE Development Kit (JDK), JDK 6 Update 12 or higher
  Please download the Java SE SDK for your system at
  http://java.sun.com/javaee/downloads/.

- Apache Tomcat server Version 6.0.18 or higher
  Please download and install the Apache Tomcat Server 6.0.18 here
  http://tomcat.apache.org/download-60.cgi and follow the instructions
  of the RUNNING.txt in your apache tomcat directory. There you will also find
  the information on how to configure and start the apache tomcat server.

**Securing the Web Service**

The simpliest way to secure the web service is by configuring basic
HTTP-Authentication which can be done either in the tomcat application server
or any web server which you are using as frontend server.

Using the Apache2 webserver as frontend, you can configure the
HTTP-Authentication as follows (example for Linux):

1) Create a password file for a new user  

    htpasswd -c /usr/local/apache2/pwds newuser

2) Configure access restriction in your httpd.conf, e.g. in

    /usr/local/apache2/httpd.conf
  
restricting the access to the axis2 location (depends on the path):

    <Location /axis2>
        AuthName "only for registered users"
        AuthType Basic
        AuthUserFile "/usr/local/apache2/pwds"
        <Limit GET>
            require valid-user
        </Limit>
    </Location>

Also in the Apache Tomcat web server you can secure the web service
via basic HTTP-Authentication (example for Linux):

1) Configure the web.xml of the axis2 web application:

    /usr/local/java/apache-tomcat-6.0.18/webapps/axis2/WEB-INF/web.xml

and add the following:

      <web-app>
        <security-constraint>
          <web-resource-collection>
              <web-resource-name>secured services</web-resource-name>
              <url-pattern>/services/*</url-pattern>
          </web-resource-collection>
          <auth-constraint>
              <role-name>webservice</role-name>
          </auth-constraint>
        </security-constraint>
        <login-config>
          <auth-method>BASIC</auth-method>
          <realm-name>webservice</realm-name>
        </login-config>
     </web-app>

2) This requires the user "webservice" to be defined in the tomcat-users.xml:

    /usr/local/java/apache-tomcat-6.0.18/conf/tomcat-users.xml

as follows:

    <?xml version="1.0" encoding="utf-8">
    <tomcat-users>
        <role rolename="webservice"/>
        <user username="tomcat" password="somepassword" roles="webservice"/>
    </tomcat-users>
