Solution
Architecture
Setup and run
clone https://github.com/maris239/shopping-component-ms.git

Import shopping-cart-service project to IDE

OR

cd shopping-cart-service and mvn compile

Run as a conventional SpringBoot application though IDE or command line.

java -jar target/shopping-cart-service-0.0.1-SNAPSHOT.jar
java -jar -Dspring.profiles.active=standalone target/shopping-cart-service-0.0.1-SNAPSHOT.jar

OR

Use Docker

docker pull dawalk/shopping-cart-service
docker run -t dawalk/shopping-cart-service
If require h2-console run

docker run -p 8080:8080 -t dawalk/shopping-cart-service
Profiles
For demonstration purpose, following profiles are configured. Default profile is configured for demonstration of embedded ActiveMQ broker.

Default - Embedded ActiveMQ message broker & embedded H2 DB
standalone - Standalone ActiveMQ message broker & embedded H2 DB
Activate relevant profile through JVM argument spring.profiles.active=standalone

Solution features
Good Test coverage
Solution is unit tested strongly with,

JUnit
Mockito
AssertJ
The app does't lose message if the database connections goes down
JMS Broker ActiveMQ ensures delivery of messages regardless the consistency of database connection. If any exception occurred inside the listener message will not be acknowledged and redeliver to the client again. If the message is unable to be persisted configured number of times, it will move to a Dead Queue in the ActiveMQ. That message will be persisted for further manual inquiries.

Refer to the listener implementation ItemSelectMessageReceiveService.java
maximumRedeliveries are configurable as demonstrated in application-standalone.properties

The app is able to auto create table if the table doesn't exist in database
Application uses JPA and default spring-hibernate configuration therefore it doesn't expect a schema to be existed. While app generates schema on startup if doesn't exist it also loads test data with the help of data.sql.

Self healing from database issue without human intervention
No human intervention is required for database. In production database shall be run in a cluster for better availability and scalability.

Good fault tolerate
Several replicas/nodes of this microservice shall be configured to run in production with the support of orchestration tools such as DockerSwarm or Kubernetes. In a failure of one or more nodes still the system is capable of running. Because of the usage of Message Queue messages will be processed consistently when a node becomes available.

Containerized
docker pull dawalk/shopping-cart-service
docker run -t dawalk/shopping-cart-service
If require h2-console run

docker run -p 8080:8080 -t dawalk/shopping-cart-serviced-service
