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
