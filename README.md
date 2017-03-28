# hippo-on-spring-boot

**hippo-on-spring-boot**, a demo project to wrap a Hippo project into single JAR executable Spring Boot application.

This project uses [hipshoot Spring Boot Support library](http://hipshoot.forge.onehippo.org/hipshoot-spring-boot-support/index.html)
to wrap a Hippo project into a Spring Boot application package.

In summary,
- This demo project was simply generated by following [Getting Started with Hippo CMS](https://www.onehippo.org/trails/getting-started/hippo-essentials-getting-started.html).
- Simply added some built-in example features using Essentials such as News, Events, etc.
- Added [spring-boot-deploy](spring-boot-deploy/) Maven submodule which lets us wrap Hippo project into single JAR executable Spring Boot application
  by following [hipshoot Spring Boot Support library](http://hipshoot.forge.onehippo.org/hipshoot-spring-boot-support/index.html).
- Added [test.sh](spring-boot-deploy/test.sh) and [test.bat](spring-boot-deploy/test.bat) to show how to execute the single executable JAR.
- That's it.

## Build

```bash
$ mvn clean package
```

## Run

```bash
$ cd spring-boot-deploy
$ ./test.sh
```

Or, on Windows,

```bash
> cd spring-boot-deploy
> test.bat
```


See [test.sh](spring-boot-deploy/test.sh) or [test.bat](spring-boot-deploy/test.bat).
It's just a simple ```java``` command line.


You can simply type Ctrl-C to stop it.

## Test

Visit [http://localhost:8080/site/](http://localhost:8080/site/) and
[http://localhost:8080/cms/](http://localhost:8080/cms/).

## Default Application Configuration

See [application.yml](spring-boot-deploy/src/main/resources/application.yml).

## Deploying to the cloud (e.g, Cloud Foundry, Heroku, etc.)

See [Deploying to the cloud](http://docs.spring.io/spring-boot/docs/current/reference/html/cloud-deployment.html) in Spring Boot documentation for detail.

You could use a very simple command like this when deploying onto Cloud Foundry, for example:

```bash
$ cd spring-boot-deploy
$ cf login -a https://api.run.pivotal.io
$ cf push hippo-on-spring-boot -f manifest.yml -p target/hippo-on-spring-boot-spring-boot-deploy-0.1.0-SNAPSHOT.jar -t 180
```

To stop the application: ```cf stop spring-boot-deploy```

To delete the application: ```cf delete spring-boot-deploy```

You can also monitor the application through PCF Console (e.g, https://console.run.pivotal.io/).

After deployment, for example, you can visit:

  - CMS: https://spring-boot-deploy.cfapps.io/cms/
  - SITE: https://spring-boot-deploy.cfapps.io/site/


## Option for Deferred Initialization of RepositoryServlet

Sometimes, you might want to start up the servlet container fast enough without waiting for the Hippo ```RepositoryServlet``` to complete its long initialization steps (such as storage and lucene index recreation). If its initialization time is more than some threshold (e.g, 60 seconds by default in CloudFoundry), it could cause failing of the **push**.

So, in this demo project, the **Repository** servlet in [cms/src/main/webapp/WEB-INF/web.xml](cms/src/main/webapp/WEB-INF/web.xml#L159:L168) was replaced by [DeferredInitDelegatingServlet](http://hipshoot.forge.onehippo.org/apidocs/org/onehippo/forge/hipshoot/spring/boot/support/servlet/DeferredInitDelegatingServlet.html) for *deferred asynchronous initialization*. This way, you can start up the servlet container fast enough without having to wait for the **RepositoryServlet** to initialize synchronously. This optional feature can be very helpful when deploying onto cloud based platform such as CloudFoundry.
