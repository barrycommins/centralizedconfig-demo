#Centralized Config Demo

Demo of centralized configuration with [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/spring-cloud-config.html)

This demo includes encryption of properties in the config repo, using Spring Cloud Config Server's [encryption and decryption mechanism](https://cloud.spring.io/spring-cloud-config/spring-cloud-config.html#_encryption_and_decryption)


Both encryption and decryption in this case are handled by the server, so additional security would be required.
E.g. Firewalling the config server so it can only be accessed from the config clients, or through Spring Security.


The `message` property in `application.properties` in [Centralized Config repo](https://github.com/barrycommins/centralizedconfig-repo) has been encrypted with public key stored in the bundled keystore, using the `/encrypt` endpoint on the config server.


The connection to the server should also be over SSL, so that the decrypted properties are not visible in transit.
