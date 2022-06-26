# LinkedIn Learning - Spring with Spring Boot
Development Environment Setup
```
# Powershell
echo $env:JAVA_HOME
C:\Users\ben\.jdks\openjdk-18.0.1.1

echo $env:MAVEN_HOME
C:\Users\ben\.mvn\apache-maven-3.8.6

mvn --version
Apache Maven 3.8.6 (84538c9988a25aec085021c365c560670ad80f63)
Maven home: C:\Users\ben\.mvn\apache-maven-3.8.6
Java version: 18.0.1.1, vendor: Oracle Corporation, runtime: C:\Users\ben\.jdks\openjdk-18.0.1.1
Default locale: en_US, platform encoding: UTF-8
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

## Maven package : build/run/prep
```
mvn package
[INFO] Scanning for projects...
[INFO]
[INFO] --------------------< com.example:spring-init-demo >--------------------
[INFO] Building spring-init-demo 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
...
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  46.509 s
[INFO] Finished at: 2022-06-26T08:19:28-05:00
[INFO] ------------------------------------------------------------------------
```

## Java run direct
```
java -jar .\target\spring-init-demo-0.0.1-SNAPSHOT.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.7.1)

2022-06-26 08:25:20.178  INFO 12916 --- [           main] c.e.s.SpringInitDemoApplication          : Starting SpringInitDemoApplication v0.0.1-SNAPSHOT using Java 18.0.1.1 on BSPAIN-SBOOK2 with PID 12916 (C:\Users\ben\Projects\IdeaJ\spring-init-demo\target\spring-init-demo-0.0.1-SNAPSHOT.jar started by ben in C:\Users\ben\Projects\IdeaJ\spring-init-demo)
2022-06-26 08:25:20.185  INFO 12916 --- [           main] c.e.s.SpringInitDemoApplication          : No active profile set, falling back to 1 default profile: "default"
2022-06-26 08:25:21.547  INFO 12916 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data JPA repositories in DEFAULT mode.
2022-06-26 08:25:21.657  INFO 12916 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 89 ms. Found 1 JPA repository interfaces.
2022-06-26 08:25:23.221  INFO 12916 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2022-06-26 08:25:23.238  INFO 12916 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2022-06-26 08:25:23.239  INFO 12916 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.64]
2022-06-26 08:25:23.516  INFO 12916 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2022-06-26 08:25:23.517  INFO 12916 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 3175 ms
```

## Dev Tips
- Anytime adding a dependency (editing `pom.xml`), be sure to sync IntelliJ

# Chapter Runtime Behavior Notes

## Chapter 2

### Serving Static Webpage Content
Observe the port that Tomcat connects too
```
Tomcat initialized with port(s): 8080 (http)
```
Browser should show the initial `src/main/resources/static/index.html` from this server.

### Auto seeding, working with in memory Database
Spring will leverage `data.sql` and `schema.sql` to seed an initial database.  `src/main/resources/application.properties` contains `ddl` directive that indicates schema is already present, and should not be generated from entity classes.

```
2022-06-26 08:25:23.758  INFO 12916 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2022-06-26 08:25:24.215  INFO 12916 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
2022-06-26 08:25:24.286 DEBUG 12916 --- [           main] o.s.jdbc.datasource.init.ScriptUtils     : Executing SQL script from URL [jar:file:/C:/Users/ben/Projects/IdeaJ/spring-init-demo/target/spring-init-demo-0.0.1-SNAPSHOT.jar!/BOOT-INF/classes!/schema.sql]
2022-06-26 08:25:24.329 DEBUG 12916 --- [           main] o.s.jdbc.datasource.init.ScriptUtils     : 0 returned as update count for SQL: CREATE TABLE ROOM( ROOM_ID BIGSERIAL PRIMARY KEY, NAME VARCHAR(16) NOT NULL, ROOM_NUMBER CHAR(2) NOT NULL UNIQUE, BED_INFO CHAR(2) NOT NULL )
2022-06-26 08:25:24.346 DEBUG 12916 --- [           main] o.s.jdbc.datasource.init.ScriptUtils     : 0 returned as update count for SQL: CREATE TABLE GUEST( GUEST_ID BIGSERIAL PRIMARY KEY, FIRST_NAME VARCHAR(64), LAST_NAME VARCHAR(64), EMAIL_ADDRESS VARCHAR(64), ADDRESS VARCHAR(64), COUNTRY VARCHAR(32), STATE VARCHAR(12), PHONE_NUMBER VARCHAR(24) )
2022-06-26 08:25:24.348 DEBUG 12916 --- [           main] o.s.jdbc.datasource.init.ScriptUtils     : 0 returned as update count for SQL: CREATE TABLE RESERVATION( RESERVATION_ID BIGSERIAL PRIMARY KEY, ROOM_ID BIGINT NOT NULL, GUEST_ID BIGINT NOT NULL, RES_DATE DATE )
2022-06-26 08:25:24.376 DEBUG 12916 --- [           main] o.s.jdbc.datasource.init.ScriptUtils     : 0 returned as update count for SQL: ALTER TABLE RESERVATION ADD FOREIGN KEY (ROOM_ID) REFERENCES ROOM(ROOM_ID)
```

`src/main/java/com/example/springinitdemo/util/AppStartupEvent.java` contains a `onApplicationEvent` Listener, which will automatically test data fetching on app startup.

```
2022-06-26 08:25:28.756  INFO 12916 --- [           main] c.e.s.SpringInitDemoApplication          : Started SpringInitDemoApplication in 9.646 seconds (JVM running for 10.969)
Room{id=1, name='Piccadilly', roomNumber='P1', bedInfo='1Q'}
Room{id=2, name='Piccadilly', roomNumber='P2', bedInfo='1Q'}
Room{id=3, name='Piccadilly', roomNumber='P3', bedInfo='1Q'}
```

## Chapter 3

### Spring IoC behaviors
`src/main/java/com/example/springinitdemo/util/DateUtils.java` has been decorated as a `@Component`.  This is the indicator to Spring to create it as singleton via IoC.  `AppStartupEvent` defines this now as a dependency in it's ctor:
```
    public AppStartupEvent(ReservationService reservationService, DateUtils dateUtils) {
        this.reservationService = reservationService;
        this.dateUtils = dateUtils;
    }
```

Now, Spring will handle providing the singleton `DateUtils` instance to this when necessary.

The chapter also now includes a `@Service` annotation on `src/main/java/com/example/springinitdemo/business/ReservationService.java` which acts in a similar way.  (The author indicated that `@Service` allows for more features, via Abstract Object Programming, such as common areas for Tracing, Auditing, etc... using these features)

This was a very powerful example of succeeding control fully to the IoC system (vs my own tendency to construct objects in my own `main` method, and pass them through ctor chains myself.)