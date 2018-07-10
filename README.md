DLock
========
[![Apache License 2](https://img.shields.io/badge/license-ASF2-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0.txt) [![Build Status](https://travis-ci.org/yusufaytas/dlock.png?branch=master)](https://travis-ci.org/yusufaytas/dlock)

DLock is a library to get a distributed interval lock. An Interval Lock is a lock where the acquirer acquires the lock for a certain amount of time. Once the lock is acquired, it won't be released till the interval ends.

Feedback and pull-requests are welcome!
+ [Usage](#usage)
+ [Lock Implementations](#lock-implementations)
  - [Postgres](#postgres)
+ [Contributing](#contributing)

# Usage
## Requirements and dependencies
* Java 8
* slf4j-api

Note that, there should be only one global lock.

## Import project
#### maven
```xml
<dependency>
    <groupId>com.yusufaytas.dlock</groupId>
    <artifactId>dlock-spring</artifactId>
    <version>0.1</version>
</dependency>
<dependency>
    <groupId>com.yusufaytas.dlock</groupId>
    <artifactId>dlock-jdbc</artifactId>
    <version>0.1</version>
</dependency>
```
or you can import all
```xml
<dependency>
    <groupId>com.yusufaytas.dlock</groupId>
    <artifactId>dlock-all</artifactId>
    <version>0.1</version>
</dependency>
```
#### gradle
```groovy
compile 'com.yusufaytas.dlock:dlock-spring:0.1'
compile 'com.yusufaytas.dlock:dlock-jdbc:0.1'
```
or you can import all
```groovy
compile 'com.yusufaytas.dlock:dlock-all:0.1'
```
## Add a Interval Lock Support
#### Spring Bean Config
An example lock support for Postgres can be added as follows
```xml
<!-- A bean for the lock implementation. Note that there should be only one global implementation-->
<bean id="postgresLock" class="com.yusufaytas.dlock.jdbc.PostgresIntervalLock">
  <constructor-arg type="javax.sql.DataSource" ref="lockDataSource"/>
</bean>
<!-- The lock gets auto-registered to the registrar -->
<bean id="lockRegistrar" class="com.yusufaytas.dlock.spring.IntervalLockRegistrar"/>
```
#### Java Code
```java
@Scheduled(cron = "*/1 * * * * *")
@TryLock(name = "example", owner = "dlock", lockFor = ONE_MINUTE)
public void exampleLock() {
  System.out.println("lock works");
}
```
# Lock Implementations
##Jdbc
You need to execute the DDL at the target database with appropriate permissions to make lock work.
### Postgres
We insert into postgres if there doesn't exist a lock. Please checkout the Postgres DDL.
### MySQL
We get an exclusive lock on the lock table and insert a new lock if it a conflicting lock doesn't exit. Please checkout the MySQL DDL.