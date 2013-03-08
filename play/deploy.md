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


## Apache as a front proxy to allow transparent upgrade of your application
The basic idea is to run two Play instances of your web application and let the front-end proxy
load-balance them. In case one is not available, it will forward all the requests to the available one.

Let’s start the same Play application two times: one on port 9999 and one on port 9998.

```$ start -Dhttp.port=9998
$ start -Dhttp.port=9999```
Now, let’s configure our Apache web server to have a load balancer.

In Apache, I have the following configuration:

<VirtualHost mysuperwebapp.com:80>
  ServerName mysuperwebapp.com
  <Location /balancer-manager>
    SetHandler balancer-manager
    Order Deny,Allow
    Deny from all
    Allow from .mysuperwebapp.com
  </Location>
  <Proxy balancer://mycluster>
    BalancerMember http://localhost:9999
    BalancerMember http://localhost:9998 status=+H
  </Proxy>
  <Proxy *>
    Order Allow,Deny
    Allow From All
  </Proxy>
  ProxyPreserveHost On
  ProxyPass /balancer-manager !
  ProxyPass / balancer://mycluster/
  ProxyPassReverse / http://localhost:9999/
  ProxyPassReverse / http://localhost:9998/
</VirtualHost>
The important part is balancer://mycluster. This declares a load balancer. The +H option means that the second Play application is on stand-by. But you can also instruct it to load-balance.

Apache also provides a way to view the status of your cluster. Simply point your browser to /balancer-manager to view the current status of your clusters.

Because Play is completely stateless you don’t have to manage sessions between the 2 clusters. You can actually easily scale to more than 2 Play instances.
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


## The basic idea is to run two Play instances of your web application and let the front-end proxy 
load-balance them. In case one is not available, it will forward all the requests to the available one.

Let’s start the same Play application two times: one on port 9999 and one on port 9998.

```bash 
$ start -Dhttp.port=9998
$ start -Dhttp.port=9999
```

Now, let’s configure our Apache web server to have a load balancer.

In Apache, I have the following configuration:
```xml
<VirtualHost mysuperwebapp.com:80>
  ServerName mysuperwebapp.com
  <Location /balancer-manager>
    SetHandler balancer-manager
    Order Deny,Allow
    Deny from all
    Allow from .mysuperwebapp.com
  </Location>
  <Proxy balancer://mycluster>
    BalancerMember http://localhost:9999
    BalancerMember http://localhost:9998 status=+H
  </Proxy>
  <Proxy *>
    Order Allow,Deny
    Allow From All
  </Proxy>
  ProxyPreserveHost On
  ProxyPass /balancer-manager !
  ProxyPass / balancer://mycluster/
  ProxyPassReverse / http://localhost:9999/
  ProxyPassReverse / http://localhost:9998/
</VirtualHost>
```
The important part is balancer://mycluster. This declares a load balancer. 
The +H option means that the second Play application is on stand-by. But you can also instruct it to load-balance.

Apache also provides a way to view the status of your cluster. Simply point your browser to /balancer-manager to view the current status of your clusters.

Because Play is completely stateless you don’t have to manage sessions between the 2 clusters. 
You can actually easily scale to more than 2 Play instances.
