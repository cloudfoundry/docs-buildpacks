---
title: Configure Service Connections for Spring
---

Cloud Foundry provides extensive support for connecting a Spring application to services such as MySQL, PostgreSQL, MongoDB, Redis, and RabbitMQ. In many cases, Cloud Foundry can automatically configure a Spring application without any code changes. For more advanced cases, you can control service connection parameters yourself.

## <a id='auto'></a>Auto-Reconfiguration ##

If your Spring application requires services (such as a relational database or messaging system), you might be able to deploy your application to Cloud Foundry without changing any code. In this case, Cloud Foundry automatically re-configures the relevant bean definitions to bind them to cloud services.

Cloud Foundry auto-reconfigures applications only if the following items are true for your application:

* Only one service instance of a given service type is bound to the application. In this context, MySQL and PostgreSQL are considered the same service type (relational database), so if both a MySQL and a PostgreSQL service are bound to the application, auto-reconfiguration will not occur.
* Only one bean of a matching type is in the Spring application context. For example, you can have only one bean of type `javax.sql.DataSource`.

With auto-reconfiguration, Cloud Foundry creates the database or connection factory bean itself, using its own values for properties such as host, port, username and so on. For example, if you have a single `javax.sql.DataSource` bean in your application context that Cloud Foundry auto-reconfigures and binds to its own database service, Cloud Foundry doesn’t use the username, password and driver URL you originally specified. Rather, it uses its own internal values. This is transparent to the application, which really only cares about having a relational database to which it can write data but doesn’t really care what the specific properties are that created the database. Also note that if you have customized the configuration of a service (such as the pool size or connection properties), Cloud Foundry auto-reconfiguration ignores the customizations.

For more information on auto-reconfiguration of specific services types, see the [Service-specific Details](#services) section.

## <a id='manual'></a>Manual Configuration ##

Sometimes you may not be able to take advantage of Cloud Foundry's
auto-reconfiguration of your Spring application. This may be because you have
multiple services of a given type or because you'd like to have more control over the
configuration. In either case, you need to include the `spring-cloud` library in your
list of application dependencies. Update your application Maven `pom.xml` or Gradle
`build.gradle`file to include a dependency on the
`org.springframework.cloud:spring-service-connector` and
`org.springframework.cloud:cloudfoundry-connector` artifacts. For example, if you use
Maven to build your application, the following `pom.xml` snippet shows how to add this
dependency.

```xml
<dependencies>
	<dependency>
	    <groupId>org.springframework.cloud</groupId>
	    <artifactId>cloudfoundry-connector</artifactId>
	    <version>1.0.0</version>
	</dependency>
	<dependency>
	    <groupId>org.springframework.cloud</groupId>
	    <artifactId>spring-service-connector</artifactId>
	    <version>1.0.0</version>
	 </dependency>
</dependencies>
```

You also need to update your application build file to include the Spring Framework Milestone repository. The following `pom.xml` snippet shows how to do this for Maven:

```xml
<repositories>
    <repository>
	   <id>org.springframework.maven.milestone</id>
	   <name>Spring Maven Milestone Repository</name>
	   <url>http://repo.spring.io/milestone</url>
	 </repository>
	 ...
</repositories>
```

### <a id='javaconfig'></a>Java Configuration ###
Typical use of Java config involves extending the `AbstractCloudConfig` class and
adding methods with the `@Bean` annotation to create beans for services. Apps
migrating from
[auto-reconfiguration](http://spring.io/blog/2011/11/04/using-cloud-foundry-services-with-spring-part-2-auto-reconfiguration/) might first try [the service-scanning approach](#scan-services) until they need more explicit control. Java config
also offers a way to expose application and service properties, should you choose to
take a lower level access in creating service connectors yourself (or for debugging
purposes, for example).

#### <a id='create-service-beans'></a>Creating Service Beans ####
In the following example, the configuration creates a `DataSource` bean connecting to
the only relational database service bound to the app (it fails if there is no
such unique service). It also creates a `MongoDbFactory` bean, again, connecting to
the only Mongodb service bound to the app. Check Javadoc for
`AbstractCloudConfig` for ways to connect to other services.

```java
class CloudConfig extends AbstractCloudConfig {
	  @Bean
	    public DataSource inventoryDataSource() {
	      return connectionFactory().dataSource();
	   }
	   ... more beans to obtain service connectors
}
```

The bean names match the method names unless you specify an explicit value to the
annotation such as `@Bean("inventory-service")` (this follows how Spring's Java
configuration works).

If you have more than one service of a type bound to the app or want to have an
explicit control over the services to which a bean is bound, you can pass the service
names to methods such as `dataSource()` and `mongoDbFactory()` as follows:

```java
class CloudConfig extends AbstractCloudConfig {

	@Bean
    public DataSource inventoryDataSource() {
       return connectionFactory().dataSource("inventory-db-service");
    }

  @Bean
   public MongoDbFactory documentMongoDbFactory() {
        return connectionFactory().mongoDbFactory("document-service");
    }

   ... more beans to obtain service connectors

}
```

Method such as `dataSource()` come in a additional overloaded variant that offer
specifying configuration options such as the pooling parameters. See Javadoc
for more details.

#### <a id='connect-gen-services'></a>Connecting to Generic Services ####
Java config supports access to generic services (that don't have a directly mapped
method--typical for a newly introduced service or connecting to a private service in
private PaaS) through the `service()` method. It follows the same pattern as the
`dataSource()`, except it allows supplying the connector type as an additional
parameters.

#### <a id='scan-services'></a>Scanning for Services ####
You can scan for each bound service using the `@ServiceScan` annotation as follows
(conceptually similar to the @ComponentScan annotation in Spring):

```java
@Configuration
@ServiceScan
class CloudConfig {

}
```
Here, one bean of the appropriate type (`DataSource` for a relational database
service, for example) is created. Each created bean will have the `id` matching
the corresponding service name. You can then inject such beans using auto-wiring:

```java
@Autowired DataSource inventoryDb;
```

If the app is bound to more than one services of a type, you can use the `@Qualifier`
 annotation supplying it the name of the service as in the following code:

```java
@Autowired @Qualifier("inventory-db") DataSource inventoryDb;
@Autowired @Qualifier("shipping-db") DataSource shippingDb;
```

#### <a id='access-service-providers'></a>Accessing Service Properties ####
You can expose raw properties for all services and the app through a bean as follows:

```java
class CloudPropertiesConfig extends AbstractCloudConfig {

   @Bean
   public Properties cloudProperties() {
       return properties();
   }

}
```

## <a id='cloud-profiles'></a>Cloud Profile ##
Spring Framework versions 3.1 and above support bean definition profiles as a way to conditionalize the application configuration so that only specific bean definitions are activated when a certain condition is true. Setting up such profiles makes your application portable to many different environments so that you do not have to manually change the configuration when you deploy it to, for example, your local environment and then to Cloud Foundry.

See the Spring Framework documentation for additional information about using Spring bean definition profiles.

When you deploy a Spring application to Cloud Foundry, Cloud Foundry automatically enables the `cloud` profile.

### <a id='cloud-profiles-java'></a>Profiles in Java Configuration ###

The `@Profile` annotation can be placed on `@Configuration` classes in a Spring
application to set conditions under which configuration classes are invoked. By using
the `default` and `cloud` profiles to determine whether the application is running on
CloudFoundry or not, your Java configuration can support both local and cloud
deployments using Java configuration classes like these:

```
public class Configuration {
	@Configuration
    @Profile("cloud")
    static class CloudConfiguration {

        @Bean
        public DataSource dataSource() {
            CloudEnvironment cloudEnvironment = new CloudEnvironment();
            RdbmsServiceInfo serviceInfo = cloudEnvironment.getServiceInfo("my-postgres", RdbmsServiceInfo.class);
            RdbmsServiceCreator serviceCreator = new RdbmsServiceCreator();
            return serviceCreator.createService(serviceInfo);
        }
    }

  @Configuration
    @Profile("default")
    static class LocalConfiguration {

	 @Bean
	       public DataSource dataSource() {
	           BasicDataSource dataSource = new BasicDataSource();
	           dataSource.setUrl("jdbc:postgresql://localhost/db");
	           dataSource.setDriverClassName("org.postgresql.Driver");
	           dataSource.setUsername("postgres");
	           dataSource.setPassword("postgres");
	           return dataSource;
	     }

	 }

}
```
## <a id='properties'></a>Property Placeholders ##
Cloud Foundry exposes a number of application and service properties directly into its
deployed applications. The properties exposed by Cloud Foundry include basic
information about the application, such as its name and the cloud provider, and
detailed connection information for all services currently bound to the application.

Service properties generally take one of the following forms:

```bash
 cloud.services.{service-name}.connection.{property}
 cloud.services.{service-name}.{property}
```

where `{service-name}` refers to the name you gave the service when you bound it to your application at deploy time, and `{property}` is a field in the credentials section of the `VCAP_SERVICES` environment variable.

For example, assume that you created a Postgres service called `my-postgres` and then bound it to your application. Assume also that this service exposes credentials in `VCAP_SERVICES` as discrete fields. Cloud Foundry exposes the following properties about this service:

```bash
cloud.services.my-postgres.connection.hostname
cloud.services.my-postgres.connection.name
cloud.services.my-postgres.connection.password
cloud.services.my-postgres.connection.port
cloud.services.my-postgres.connection.username
cloud.services.my-postgres.plan
cloud.services.my-postgres.type
```

If the service exposed the credentials as a single `uri` field, then the following properties would be set up:

```bash
cloud.services.my-postgres.connection.uri
cloud.services.my-postgres.plan
cloud.services.my-postgres.type
```

For convenience, if you have bound just one service of a given type to your
application, Cloud Foundry creates an alias based on the service type instead of the
service name. For example, if only one MySQL service is bound to an application, the
properties takes the form `cloud.services.mysql.connection.{property}`. Cloud
Foundry uses the following aliases in this case:

* `mysql`
* `postgresql`
* `mongodb`
* `redis`
* `rabbitmq`

A Spring application can take advantage of these Cloud Foundry properties using the
property placeholder mechanism. For example, assume that you have bound a MySQL
service called `spring-mysql` to your application. Your application requires a `c3p0`
connection pool instead of the connection pool provided by Cloud Foundry, but you want
to use the same connection properties defined by Cloud Foundry for the MySQL service -
in particular the username, password and JDBC URL.

The following table lists all the application properties that Cloud Foundry exposes to deployed applications.

| Property                 | Description
| ------------------------ |------------
| `cloud.application.name` | The name provided when the application was pushed to CloudFoundry.
| `cloud.provider.url`     | The URL of the cloud hosting the application, such as `cloudfoundry.com`.

The service properties that are exposed for each type of service are listed in the [Service-specific Details](#services) section.

## <a id='services'></a>Service-Specific Details ##
The following sections describe Spring auto-reconfiguration and manual configuration for the services supported by Cloud Foundry.

### <a id='rdbms'></a>MySQL and Postgres ###

#### <a id='mysql-auto-reconfig'></a>Auto-Reconfiguration ####
Auto-reconfiguration occurs if Cloud Foundry detects a `javax.sql.DataSource` bean in
the Spring application context. The following snippet of a Spring application context
file shows an example of defining this type of bean which Cloud Foundry detects
and potentially auto-reconfigure:

```xml
<bean class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close" id="dataSource">
    <property name="driverClassName" value="org.h2.Driver" />
    <property name="url" value="jdbc:h2:mem:" />
    <property name="username" value="sa" />
    <property name="password" value="" />
</bean>
```

The relational database that Cloud Foundry actually uses depends on the service instance you explicitly bind to your application when you deploy it: MySQL or Postgres. Cloud Foundry creates either a commons DBCP or Tomcat datasource depending on which datasource implementation it finds on the classpath.

Cloud Foundry internally generates values for the following properties: `driverClassName`, `url`, `username`, `password`, `validationQuery`.

#### <a id='mysql-man-java-reconfig'></a>Manual Configuration in Java ####

To configure a database service in Java configuration, create a `@Configuration` class with a `@Bean` method to return a `javax.sql.DataSource` bean. The bean can be created by helper classes in the `cloudfoundry-runtime` library, as shown here:

```
@Configuration
public class DataSourceConfig {
    @Bean
    public DataSource dataSource() {
        CloudEnvironment cloudEnvironment = new CloudEnvironment();
        RdbmsServiceInfo serviceInfo = cloudEnvironment.getServiceInfo("my-postgres", RdbmsServiceInfo.class);
        RdbmsServiceCreator serviceCreator = new RdbmsServiceCreator();
        return serviceCreator.createService(serviceInfo);
    }
}
```
### <a id='mongodb'></a>MongoDB ###

#### <a id='mongodb-auto-reconfig'></a>Auto-Reconfiguration ####
You must use Spring Data MongoDB 1.0 M4 or later for auto-reconfiguration to work.

Auto-reconfiguration occurs if Cloud Foundry detects a `org.springframework.data.document.mongodb.MongoDbFactory` bean in the Spring application context. The following snippet of a Spring XML application context file shows an example of defining this type of bean which Cloud Foundry will detect and potentially auto-reconfigure:

```xml
<mongo:db-factory
    id="mongoDbFactory"
    dbname="pwdtest"
    host="127.0.0.1"
    port="1234"
    username="test_user"
    password="test_pass" />
```

Cloud Foundry creates a `SimpleMongoDbFactory` with its own values for the following properties: `host`, `port`, `username`, `password`, `dbname`.

#### <a id='mongodb-man-java-reconfig'></a>Manual Configuration in Java ####

To configure a MongoDB service in Java configuration, create a `@Configuration` class with a `@Bean` method to return a `org.springframework.data.mongodb.MongoDbFactory` bean (from Spring Data MongoDB). The bean can be created by helper classes in the `cloudfoundry-runtime` library, as shown here:

```
@Configuration
public class MongoConfig {

    @Bean
    public MongoDbFactory mongoDbFactory() {
        CloudEnvironment cloudEnvironment = new CloudEnvironment();
        MongoServiceInfo serviceInfo = cloudEnvironment.getServiceInfo("my-mongodb", MongoServiceInfo.class);
        MongoServiceCreator serviceCreator = new MongoServiceCreator();
        return serviceCreator.createService(serviceInfo.get);
    }

    @Bean
    public MongoTemplate mongoTemplate() {
        return new MongoTemplate(mongoDbFactory());
    }

}
````
### <a id='redis'></a>Redis ###

#### <a id='redis-auto-reconfig'></a>Auto-Configuration ####
You must be using Spring Data Redis 1.0 M4 or later for auto-configuration to work.

Auto-configuration occurs if Cloud Foundry detects a `org.springframework.data.redis.connection.RedisConnectionFactory` bean in the Spring application context. The following snippet of a Spring XML application context file shows an example of defining this type of bean which Cloud Foundry will detect and potentially auto-configure:

```xml
<bean id="redis"
  class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory"
  p:hostName="localhost" p:port="6379" />
```

Cloud Foundry creates a `JedisConnectionFactory` with its own values for the following properties: `host`, `port`, `password`. This means that you must package the Jedis JAR in your application. Cloud Foundry does not currently support the JRedis and RJC implementations.

#### <a id='redis-man-java-reconfig'></a>Manual Configuration in Java ####
To configure a Redis service in Java configuration, create a `@Configuration` class with a `@Bean` method to return a `org.springframework.data.redis.connection.RedisConnectionFactory` bean (from Spring Data Redis). The bean can be created by helper classes in the `spring-cloud` library, as shown here:

```
@Configuration
public class RedisConfig {

    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        CloudEnvironment cloudEnvironment = new CloudEnvironment();
        RedisServiceInfo serviceInfo = cloudEnvironment.getServiceInfo("my-redis", RedisServiceInfo.class);
        RedisServiceCreator serviceCreator = new RedisServiceCreator();
        return serviceCreator.createService(serviceInfo);
    }

    @Bean
    public RedisTemplate redisTemplate() {
        return new StringRedisTemplate(redisConnectionFactory());
    }

}
```
### <a id='rabbitmq'></a>RabbitMQ ###

#### <a id='rabbitmq-auto-reconfig'></a>Auto-Configuration ####
You must be using Spring AMQP 1.0 or later for auto-configuration to work. Spring AMQP provides publishing, multi-threaded consumer generation, and message conversion. It also facilitates management of AMQP resources while promoting dependency injection and declarative configuration.

Auto-configuration occurs if Cloud Foundry detects a `org.springframework.amqp.rabbit.connection.ConnectionFactory` bean in the Spring application context. The following snippet of a Spring application context file shows an example of defining this type of bean which Cloud Foundry will detect and potentially auto-configure:

```xml
<rabbit:connection-factory
    id="rabbitConnectionFactory"
    host="localhost"
    password="testpwd"
    port="1238"
    username="testuser"
    virtual-host="virthost" />
```

Cloud Foundry creates a `org.springframework.amqp.rabbit.connection.CachingConnectionFactory` with its own values for the following properties: `host`, `virtual-host`, `port`, `username`, `password`.

#### <a id='rabbitmq-man-java-reconfig'></a>Manual Configuration in Java ####
To configure a RabbitMQ service in Java configuration, create a
`@Configuration` class with a `@Bean` method to return a
`org.springframework.amqp.rabbit.connection.ConnectionFactory` bean (from the Spring
AMQP library). The bean can be created by helper classes in the `spring-cloud`
library, as shown here:

```
@Configuration
public class RabbitConfig {
    @Bean
    public ConnectionFactory rabbitConnectionFactory() {
        CloudEnvironment cloudEnvironment = new CloudEnvironment();
        RabbitServiceInfo serviceInfo = cloudEnvironment.getServiceInfo("my-rabbit", RabbitServiceInfo.class);
        RabbitServiceCreator serviceCreator = new RabbitServiceCreator();
        return serviceCreator.createService(serviceInfo);
    }

    @Bean
    public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {
        return new RabbitTemplate(connectionFactory);
    }

}
```
