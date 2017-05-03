#Centralized Config Demo

Demo of centralized configuration with [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/spring-cloud-config.html)

This demo includes encryption of properties in the config repo, using Spring Cloud Config Server's [encryption and decryption mechanism](https://cloud.spring.io/spring-cloud-config/spring-cloud-config.html#_encryption_and_decryption)

###Encryption/Decryption of properties
Both encryption and decryption in this case are handled by the server, so additional security would be required.
E.g. Firewalling the config server so it can only be accessed from the config clients, or through Spring Security.


The `message` property in `application.properties` in [Centralized Config repo](https://github.com/barrycommins/centralizedconfig-repo) has been encrypted with public key stored in the bundled keystore, using the `/encrypt` endpoint on the config server.


The connection to the server should also be over SSL, so that the decrypted properties are not visible in transit.

###Sanitizing Spring Boot Actuator endpoints
Spring Boot's Actuator endpoint only sanitize certain property names by default, 
so the decrypted value in this case would be visible in the `/env` and `/configprops` endpoints.
To change this, the following properties must be set on the client:

```properties
endpoints.configprops.keys-to-sanitize=password,secret,key,token,.*credentials.*,vcap_services,message
endpoints.env.keys-to-sanitize:=password,secret,key,token,.*credentials.*,vcap_services,message
```

It would now be show in the `/env` endpoint as, e.g.

```json
"configService:https://github.com/barrycommins/centralizedconfig-repo/config-client.yml": {
"message": "******"
```


The first 6 values are Spring Boot's default, while added "message" in this case allows the encrypted value to be masked.

###Client retry if server is unreachable
The config client is also configured to retry if the server is unreachable, which is not always emphasised in the documentation.
It is particularly useful when using something like `docker-compose`, where startup order is not guaranteed.


This is done by adding
```xml
<dependency>
    <groupId>org.springframework.retry</groupId>
    <artifactId>spring-retry</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>    
```
to the pom and setting `spring.cloud.config.fail-fast=true`
