# jettyNotes

## How to start Jetty:
```bash 
java -jar start.jar 
```
etc/jetty.xml is the default configuration file for Jetty.

Deploying Webapps:
+ Jetty can deploy web applications as WAR files(packed or unpacked), as provided or with 
  overridden configration, and with static or hot deployment.
+ Jetty scans its $JETTY_HOME/webapps directory at startup for web applications to deploy:
   + It deploys the file foo.war as a webapp at context /foo
   + It deploys the directory foo/ at context /foo. If the directory has a WEB-INF subdirectory,
     Jetty treats it as a servlet webapp, otherwise Jetty servers it only as static content.
   + If both a foo.war and a foo/ directory exist, Jetty uses the one with the most recent
     last-modified date.
   + If the webapp is called root.war or the directory is called root/ then Jetty deploys it at
     the / context.
   + If the contextXmlDir option is used and a foo.xml file exists in that dir, the
     WebAppProvider defers to the contexts Provider for actual webapp deployment.

Deploying Contexts:
+ Jetty scans the $JETTY_HOME/contexts directory at runtime for context.xml files that describe either:
   + a standard WAR file (or directory), plus additional configuration.
   + a custom Jetty context.

Using the WebAppProvider:
+ The WebAppProvider’s role is to look in the “${jetty.home}/webapps/” directory for any deployable
  applications “(such as *.war)”, and deploy them onto a context of the same name as the filenames.
+ While this is the easiest deployment mechanism, it sacrifies control over deployment specifics.

Using the ContextProvider:
+ The ContextProvider's role is to look in the ${jetty.home}/contexts/ directory for any jetty-xml formatted, deployable contexts. This deployment mechanism gives you the maximum control over the deployment.

Setting the Context Path with the ContextProvider:
To set the context path to / , follow these steps.

1. Make sure your ContextProvider-based deployments are enabled in the start.ini. That is, make sure that etc/jetty-context.xml is present.
2. Create a ${jetty.home}/contexts/mywebapp.xml that declares the <Set name="contextPath">/</Set> option.
3. If you have the etc/jetty-webapps.xml present in your start.ini, do not put your mywebapp.war in ${jetty.home}/webapps because doing so causes the WebAppProvider to also deploy the same webapp, and confuses your deployment.
4. Finally, you can see how the jetty distribution itself does this by examining ${jetty.home}/contexts/test.xml. Jetty loads the ${jetty.home}/webapps/test.war via the ContextProvider's use of the ${jetty.home}/contexts/test.xml into the / context path.

Jetty command line options:
+ There are several options that can be set on the command line when using supplied etc/jetty.xml
+ “jetty.home” => directory which contains the etc, webapps, contexts folders. No trailing slash. Default value is the directory from which you are running start.jar.
+ “jetty.host” => host for the default connector. No default value.
+ “jetty.port” => port for the default connector. Default value is 8080.
+ “jetty.logs” => directory which contains the request logs. No trailing slash. Default value is ${jetty.home}/logs
```bash 
  java -Doption1=value -Doption2=value -jar start.jar etc/jetty.xml 
```

Customizing your Jetty configuration:
+ etc/jetty.xml sets reasonable defaults, but you will want to customize Jetty to fit your needs. 
+ configuration file overview:
```xml
   <?xml version="1.0"?>
   <!DOCTYPE Configure PUBLIC "-//Mort Bay Consulting//DTD Configure//EN" "http://jetty.mortbay.org/configure.dtd">
    
   <Configure id="Server" class="org.mortbay.jetty.Server">
    
     <!-- required configuration -->
     <!-- connectors -->
     <!-- handlers -->
     <!-- webapps/contexts -->
    
     <!-- optional configuration -->
     <!-- threadpool -->
     <!-- session id manager -->
     <!-- authentication realms -->
     <!-- request logs -->
     <!-- extra server options -->
   </Configure> 
```

+ To start jetty with a customized configration file:
```bash 
      java -jar start.jar etc/myjetty.xml
```

Jetty-logging.xml:
+ You can use etc/jetty-logging.xml to take all System.out and System.err output (from any source) and 
  route it to a rolling log file. To do so, include etc/jetty-logging.xml on Jetty startup.

```bash 
  java -jar start.jar etc/jetty-logging.xml
```

