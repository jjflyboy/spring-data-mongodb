Spring Data MongoDB
======================

The primary goal of the [Spring Data](http://www.springsource.org/spring-data) project is to make it easier to build Spring-powered applications that use new data access technologies such as non-relational databases, map-reduce frameworks, and cloud based data services.

The Spring Data MongoDB aims to provide a familiar and consistent Spring-based programming model for for new datastores while retaining store-specific features and capabilities. The Spring Data MongoDB project provides integration with the MongoDB document database. Key functional areas of Spring Data MongoDB are a POJO centric model for interacting with a MongoDB DBCollection and easily writing a Repository style data access layer

Getting Help
------------

For a comprehensive treatmet of all the Spring Data MongoDB features, please refer to the The [User Guide](http://static.springsource.org/spring-data/data-mongodb/docs/current/reference/html/) 

The [JavaDocs](http://static.springsource.org/spring-data/data-mongodb/docs/current/api/) have extensive comments in them as well.

The home page of [Spring Data MongoDB](http://www.springsource.org/spring-data/mongodb) contains links to articles and other resources.

For more detailed questions, use the [forum](http://forum.springsource.org/forumdisplay.php?f=80). 

If you are new to Spring as well as to Spring Data, look for information about [Spring projects](http://www.springsource.org/projects). 


Quick Start
-----------

## MongoDB

For those in a hurry:


* Download the jar through Maven:

```xml
<dependency>
  <groupId>org.springframework.data</groupId>
  <artifactId>spring-data-mongodb</artifactId>
  <version>1.2.1.RELEASE</version>
</dependency>
```

### MongoTemplate
MongoTemplate is the central support class for Mongo database operations.  It provides

* Basic POJO mapping support to and from BSON
* Connection Affinity callback
* Exception translation into Spring's [technology agnostic DAO exception hierarchy](http://static.springsource.org/spring/docs/3.0.x/spring-framework-reference/html/dao.html#dao-exceptions).

Future plans are to support optional logging and/or exception throwing based on WriteResult return value, common map-reduce operations, GridFS operations.  A simple API for partial document updates is also planned.

### Easy Data Repository generation

To simplify the creation of data repositories a generic `Repository` interface and default implementation is provided.  Furthermore, Spring will automatically create a Repository implementation for you that adds implementations of finder methods you specify on an interface.  

The Repository interface is

```java
public interface Repository<T, ID extends Serializable> { 

  T save(T entity);

  List<T> save(Iterable<? extends T> entities);

  T findById(ID id);

  boolean exists(ID id);

  List<T> findAll();

  Long count();

  void delete(T entity);

  void delete(Iterable<? extends T> entities);

  void deleteAll();
}
```


The `MongoRepository` extends `Repository` and will in future add more Mongo specific methods.

```java
public interface MongoRepository<T, ID extends Serializable> extends Repository<T, ID> {
}
```

`SimpleMongoRepository` is the out of the box implementation of the `MongoRepository` you can use for basid CRUD operations.  

To go beyond basic CRUD, extend the `MongoRepository` interface and supply your own finder methods that follow simple naming conventions such that they can be easily converted into queries.  

For example, given a `Person` class with first and last name properties, a `PersonRepository` interface that can query for `Person` by last name and when the first name matches a regular expression is shown below

```java
public interface PersonRepository extends MongoRepository<Person, Long> {

  List<Person> findByLastname(String lastname);

  List<Person> findByFirstnameLike(String firstname);
}
```

You can have Spring automatically create a proxy for the interface as shown below:

```xml
<bean id="template" class="org.springframework.data.document.mongodb.MongoTemplate">
        <constructor-arg>
                <bean class="com.mongodb.Mongo">
                        <constructor-arg value="localhost" />
                        <constructor-arg value="27017" />
                </bean>
        </constructor-arg>
        <constructor-arg value="database" />
        <property name="defaultCollectionName" value="springdata" />
</bean>

<mongo:repositories base-package="com.acme.repository" />
```

This will find the repository interface and register a proxy object in the container.  You can use it as shown below:

``java
@Service
public class MyService {

  @Autowired
  private final PersonRepository repository;

  public void doWork() {

     repository.deleteAll();

     Person person = new Person();
     person.setFirstname("Oliver");
     person.setLastname("Gierke");
     person = repository.save(person);

     List<Person> lastNameResults = repository.findByLastname("Gierke");

     List<Person> firstNameResults = repository.findByFirstnameLike("Oli*");

 }
}
```


Contributing to Spring Data
---------------------------

Here are some ways for you to get involved in the community:

* Get involved with the Spring community on the Spring Community Forums.  Please help out on the [forum](http://forum.springsource.org/forumdisplay.php?f=80) by responding to questions and joining the debate.
* Create [JIRA](https://jira.springframework.org/browse/DATADOC) tickets for bugs and new features and comment and vote on the ones that you are interested in.  
* Github is for social coding: if you want to write code, we encourage contributions through pull requests from [forks of this repository](http://help.github.com/forking/). If you want to contribute code this way, please reference a JIRA ticket as well covering the specific issue you are addressing.
* Watch for upcoming articles on Spring by [subscribing](http://www.springsource.org/node/feed) to springframework.org

Before we accept a non-trivial patch or pull request we will need you to sign the [contributor's agreement](https://support.springsource.com/spring_committer_signup).  Signing the contributor's agreement does not grant anyone commit rights to the main repository, but it does mean that we can accept your contributions, and you will get an author credit if we do.  Active contributors might be asked to join the core team, and given the ability to merge pull requests.
