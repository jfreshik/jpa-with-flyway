# SpringBoot Flyway test project

## using docker-compose
  * run/stop mariadb 10.4 by docker-compose
    * user/pw: flyway/1234
    * db: flywaydb  
```shell script
# run
$ docker-compose up -d

# stop
$ docker-compose down
```

## Flyway init
  * property set _hibernate.hbm2ddl.auto=validate_ (hibernate.properties)
```properties
hibernate.hbm2ddl.auto=validate
```

  * create init script
    * V\<VERSION\>__\<DESCRIPTION\>.sql
    * V1__create_person.sql
    > version과 description 사이에는 언더스코어('_') 가 2개
      org.flywaydb.core.api.FlywayException: Version may only contain 0..9 and . (dot).
                                                                                               
```mysql-sql
CREATE TABLE IF NOT EXISTS `person` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `age` int(11) NOT NULL,
  `name` varchar(50) COLLATE utf8mb4_unicode_ci NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```
  * add actuator endpoint (application.yml)
    * localhost:9000/actuator/flyway
```yaml
management:
  server.port: 9000
  endpoint.health.show-details: always
  endpoints.web.exposure.include: flyway, health, info, metrics, env
```

```json
{
    "contexts": {
        "application": {
            "flywayBeans": {
                "flyway": {
                    "migrations": [
                        {
                            "type": "SQL",
                            "checksum": 1359790794,
                            "version": "1",
                            "description": "create person",
                            "script": "V1__create_person.sql",
                            "state": "SUCCESS",
                            "installedBy": "flyway",
                            "installedOn": "2020-04-05T05:32:44Z",
                            "installedRank": 1,
                            "executionTime": 45
                        }
                    ]
                }
            },
            "parentId": null
        }
    }
}
```