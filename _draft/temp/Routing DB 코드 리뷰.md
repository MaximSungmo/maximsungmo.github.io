Routing DB 코드 리뷰



1. malldatabase.yaml 의 값을 객체로 가져오기 

`com/cafe24/cmc/springboot/SpringbootApplication.java` 의 `PROPERTIES 변수`에 정의된 `classpath` 를 확인하여 yaml 파일을 읽고 `com/cafe24/cmc/springboot/config/routingdatabase/MallDatabaseProperty.java` 에서 `@ConfigurationProperties(prefix = "spring")` 를 통해 yaml 파일의 `spring : ` 내용을 찾는다. 이 후 객체의 변수명이 일치하는 `property`를 yaml에서 찾으면 동일 키밸류 값으로 매핑을 시도한다.

따라서 `com/cafe24/cmc/springboot/config/routingdatabase/DBSource.java:` 내용의 배열을 `com/cafe24/cmc/springboot/config/routingdatabase/MallDatabaseProperty.java` 를 통해 객체화시킬 수 있다. 

- com/cafe24/cmc/springboot/config/routingdatabase/MallDatabaseProperty.java

```
@Configuration
@ConfigurationProperties(prefix = "spring")
@Data
public class MallDatabaseProperty {
    private List<DBSource> mall = new ArrayList<>();
}
```



- com/cafe24/cmc/springboot/config/routingdatabase/DBSource.java

```
@Data
public class DBSource {
    private String name;
    private HikariConfig dataSource;
}
```



- malldatabase.yaml

```
spring:
  profiles: DEV
  mall:
    - name : db_gmc_welbu
      datasource :
        maximum-pool-size: 1
        minimum-idle: 1
        password: **
        jdbc-url: **      
        username: **
        driver-class-name: net.sf.log4jdbc.sql.jdbcapi.DriverSpy
    - name : db_gmc_vivevive8
      datasource :
        maximum-pool-size: 10
        minimum-idle: 1
        password: **
        jdbc-url: **      
        username: **
        driver-class-name: net.sf.log4jdbc.sql.jdbcapi.DriverSpy
    - name : db_gmc_sseoqkr7
      datasource :
        maximum-pool-size: 1
        minimum-idle: 1
        password: **
        jdbc-url: **      
        username: **
        driver-class-name: net.sf.log4jdbc.sql.jdbcapi.DriverSpy
```



2. 객체화된 database route를 위한 설정 정보 배열을 이용하여 routeDataSource를 bean으로 생성하기 

database 에 접근하기 위하여 스프링에서는 db connection pool 을 생성해놓고 있는데, 기존의 정의된 방식은 해당 폴더에 설정된 datasource를 이용하여 정적인 접근이 되었다. 이를 해결하기 위하여 `AbstractRoutingDataSource` 라는 abstract class 를 상속받은 구현체를 통하여 datasource는 동적으로 database를 바꿀 수 있다. 이를위하여 키-밸류 형태로 `database별칭 - datasource` 를 갖도록하여 이를 datasource로 셋팅해주고 `@bean` 으로 등록하면 spring의 `IOC container` 에서 이를 관리하게 된다.

즉, routing을 위한 datasource가 생성되었으며 키-밸류 형태이므로 키 값으로 접근 시 해당하는 datasouurce를 반환받도록 설정하기만 하면 된다. 

- com/cafe24/cmc/springboot/config/routingdatabase/RoutingDatabaseConfig.java

```
@Configuration
@MapperScan(basePackages = DatabaseConfig.BASE_ENTITY_PACKAGE_PREFIX + ".db_route", sqlSessionFactoryRef = "routeSqlSessionFactory")
@EnableJpaRepositories(entityManagerFactoryRef = "routeEntityManagerFactory", transactionManagerRef = "routeTransactionManager", basePackages = {DatabaseConfig.BASE_ENTITY_PACKAGE_PREFIX + ".db_route"})
public class RoutingDatabaseConfig extends DatabaseConfig {

    ...
    
    @Bean("routeDataSource")
    public DataSource clientDatasource() {

        Map<Object, Object> targetDataSources = new HashMap<>();
        mallDatabaseProperty.getMall().forEach((v)->{
            targetDataSources.put(v.getName(), createDataSource(v.getDataSource()));
        });

        RoutingDataSource dataSourceRouter = new RoutingDataSource();
        dataSourceRouter.setTargetDataSources(targetDataSources);

        return dataSourceRouter;
    }
    
    ...
}
```



3. `@Bean` 으로 등록된 routeDataSource가 database별칭으로 datasource를 찾기 

RoutingDataSource는 `AbstractRoutingDataSource` 라는 abstarct class 를 상속받아 구현된 클래스이며 이 클래스에서 동적으로 런타임 시 route 되는 datasource를 찾기 위하여  `determineCurrentLookupKey()`을 `@Override ` 시켜주면 `determineCurrentLookupKey()`의 리턴 값에 해당하는 datasource를 찾아서 db에 접근할 수 있다.

- com/cafe24/cmc/springboot/config/routingdatabase/RoutingDataSource.java

```
public class RoutingDataSource extends AbstractRoutingDataSource {

    @Override
    protected Object determineCurrentLookupKey() {

        return RoutingDatabaseContextHolder.getClientDatabase();
    }
}
```



4. Route를 위한 datasource를 1개의 thread 당(1개의 request) 변수 공유가 될 수 있도록 처리

위의 설명과 같이 route를 위한 database 의 설정을 읽고(`malldatabase.yaml`) 객체화하여 (`MallDatabaseProperty.java`) datasource를 여러개 만든 후 런타임 시 해당 datasource로 동적으로 route 시키기 위하여 `com/cafe24/cmc/springboot/config/routingdatabase/RoutingDataSource.java`  `determineCurrentLookupKey()` 에서는 현재 결정된 route를 위한 datasource에 대한 정보(`database별칭`)를 리턴해주었다.

이는 하나의 thread 당 (request) 정보가 공유될 수 있도록 ThreadLocal 변수로 처리하였다.  

따라서 하나의 thread당 routeDataSource 는 위의 `@Bean` 으로 등록한 `routeDataSource`를 설정해주었던 값의 키-밸류 중 `키`에 해당하는`database별칭`으로 datasource를 찾고 이 후 다른 서비스 또는 레포지토리에서 접근을 하더라도 동일 thread에서는 `routeDataSource`는 `database별칭`에 해당하는 datasource로 작업을 수행하게 된다. 

이는 `com/cafe24/cmc/springboot/config/routingdatabase/RoutingDatabaseContextHolder.java` 에 target이 되는 datasource의 `database별칭` 을 넣어주어 동적으로 `set()` 함수를 통해 변경이 가능하다.



- com/cafe24/cmc/springboot/config/routingdatabase/RoutingDatabaseContextHolder.java

```

public class RoutingDatabaseContextHolder {
    private static ThreadLocal<String> CONTEXT = new ThreadLocal<>();  
    
    public static void set(String clientDatabase) {

        Assert.notNull(clientDatabase, "RoutingDatabase cannot be null");
        CONTEXT.set(clientDatabase);
    }


    public static String getClientDatabase() {

        return CONTEXT.get();
    }
    ...
}
```

