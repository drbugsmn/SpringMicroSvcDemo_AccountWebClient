# Overview
This project demonstrates a web client that will consume a spring account microservice for
 various requestts.  It has the option to use a spring config server to retrieve it's config
 data.
 
 
# Configuration
SpringBoot has two places (probably more but good enough for getting set up).  These files
reside in /resources.  The first place is boostrap.[yml, properties].  This is a place 
to 'bootstrap' the spring context before it gets fired up.  The other is (whatever-name).[yml, properties]
and this file can be specified via a -D property via an environment vairable, or in the
springboot (look in WebServer.java).  The property name is 'spring.config.name', and in this app
it is called 'web-server'. It can be a .yml or a .properties file.
 
 
This app is set up for both local config and using a spring config server as a demo.
 
## Local config
Make sure everything in the /resources/bootstrap.yml are commented out.  Make sure that 
everything in /resources/web-server.yml are uncommented.  An interesting note is the local 
config is configured to host this app on port 3333, vs using port 3334 for the spring config
server version.
 
## Spring Config Server
Make sure everything in /resources/bootstrap.yml is uncommented, and everything in /resourcs/web-server.yml
are commented out.  Note the port in use will now be spring 3334.
 
### Spring config client options
#### Fail if we can't talk to the config server
spring.cloud.config.failFast=true  Set this to false if we want to keep trying to find it, will
noise up logs.  Default is 'false'
 
#### Retry config if we can't talk to the config server
spring.cloud.config.failFast=true and we need to add the following to our maven deps
 
1. spring-retry
2. spring-boot-starter-aop

This will give us a default of 6 times for attempting to retry then die.  The default behaviour 
is to retry 6 times with an initial backoff interval of 1000ms and an exponential multiplier 
of 1.1 for subsequent backoffs. You can configure these properties (and others) using 
spring.cloud.config.retry.* configuration properties.

#### Specifing what config to ask for.
The Config Service serves property sources from /{name}/{profile}/{label}, where the default 
bindings in the client app are

    "name" = ${spring.application.name}
    "profile" = ${spring.profiles.active} (actually Environment.getActiveProfiles())
    "label" = "master"

All of them can be overridden by setting spring.cloud.config.* (where * is "name", "profile" 
or "label"). The "label" is useful for rolling back to previous versions of configuration; 
with the default Config Server implementation it can be a git label, branch name or commit 
id. Label can also be provided as a comma-separated list, in which case the items in the list are tried on-by-one until one succeeds. This can be useful when working on a feature branch, for instance, when you might want to align the config label with your branch, but make it optional (e.g. spring.cloud.config.label=myfeature,develop).
 