#How to deploy PLAY framework


## Development Mode
sbt run

## Production Mode
* **sbt start**
	* Easiest way to deploy
	* The problem with the start command is that it starts the application interactively, 
	  which means that human interaction is needed, and Ctrl+D is required to detach the process. 
	  This solution is not really convenient for automated deployment.
* **sbt clean compile stage**
	* This cleans and compiles your application, retrieves the required dependencies and 
	  copies them to the target/staged directory
	* It also creates a target/start script that runs the Play server.
	* The generated start script is very simple - in fact, you could even execute the java command directly.
	
## Standalon Mode
* **sbt dist**
	* You sometimes need to build a binary version of your application and deploy it to the server 
	  without any dependencies on Play itself.
	* This produces a ZIP file containing all JAR files needed to run your application in the target folder 
	  of your application includng  a start script. You can use the generated start 
	  script to run your application.
	  
## Specifying the HTTP server address and port
```start -Dhttp.port=1234 -Dhttp.address=127.0.0.1```

## Specifying additional JVM arguments

You can specify any JVM arguments to the start script. Otherwise the default JVM settings will be used:

```start -Xms128M -Xmx512m -server```

## Specifying alternative configuration file

Using -Dconfig.resuorce will search for an alternative configuration file in the application classpath (you 
usually provide these alternative configuration files into your application conf/ directory 
before packaging).

```start -Dconfig.resource=prod.conf```

You can also specify another local configuration file not packaged into the application artifacts:

```start -Dconfig.file=/opt/conf/prod.conf```

You can also specify a configuration file to be loaded from any URL:

```start -Dconfig.url=http://conf.mycompany.com/conf/prod.conf```

## Overriding specific configuration keys

Sometimes you don’t want to specify another complete configuration file, but just override a bunch of specific keys. You can do that by specifying then as Java System properties:

```start -Dapplication.secret=verysecretkey -Ddb.default.password=toto```

## Changing the logback configuration file

You can also specify another logback configuration file via a System property

Using -Dlogger.resource
Specify another loback configuration file to be loaded from the classpath:

```$ start -Dlogger.resource=conf/prod-logger.xml```

Using -Dlogger.file
Specify another loback configuration file to be loaded from the file system:

```$ start -Dlogger.file=/opt/prod/logger.xml```

Using -Dlogger.url
Specify another loback configuration file to be loaded from an URL:

```$ start -Dlogger.url=http://conf.mycompany.com/logger.xml```




