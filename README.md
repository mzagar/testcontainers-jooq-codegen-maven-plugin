# jooq-testcontainers-codegen-maven-plugin

The `jooq-testcontainers-codegen-maven-plugin` simplifies the jOOQ code generation 
by using [Testcontainers](https://www.testcontainers.org/) and applying Flyway database migrations.

[![Build](https://github.com/sivalabs/jooq-testcontainers-codegen-maven-plugin/actions/workflows/build.yml/badge.svg)](https://github.com/sivalabs/jooq-testcontainers-codegen-maven-plugin/actions/workflows/build.yml)
![Maven Central](https://img.shields.io/maven-central/v/io.github.sivalabs/jooq-testcontainers-codegen-maven-plugin)

## Supported databases:
* Postgres
* MySQL
* MariaDB

## How to use?

1. **With PostgreSQL and Flyway migrations**

```xml
<project>
    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <testcontainers.version>1.18.0</testcontainers.version>
        <jooq-testcontainers-codegen-maven-plugin.version>0.0.2</jooq-testcontainers-codegen-maven-plugin.version>
        <jooq.version>3.18.3</jooq.version>
        <postgres.version>42.6.0</postgres.version>
    </properties>
    
    <dependencies>
        <dependency>
            <groupId>org.jooq</groupId>
            <artifactId>jooq</artifactId>
            <version>${jooq.version}</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>io.github.sivalabs</groupId>
                <artifactId>jooq-testcontainers-codegen-maven-plugin</artifactId>
                <version>${jooq-testcontainers-codegen-maven-plugin.version}</version>
                <dependencies>
                    <dependency>
                        <groupId>org.testcontainers</groupId>
                        <artifactId>postgresql</artifactId>
                        <version>${testcontainers.version}</version>
                    </dependency>
                    <dependency>
                        <groupId>org.postgresql</groupId>
                        <artifactId>postgresql</artifactId>
                        <version>${postgres.version}</version>
                    </dependency>
                </dependencies>
                <executions>
                    <execution>
                        <id>generate-jooq-sources</id>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                        <phase>generate-sources</phase>
                        <configuration>
                            <database>
                                <!--
                                "type" can be: POSTGRES, MYSQL, MARIADB
                                -->
                                <type>POSTGRES</type>
                                <!--
                                "containerImage" is optional.
                                The defaults are 
                                    POSTGRES: postgres:15.2-alpine
                                    MYSQL: mysql:8.0.33
                                    MARIADB: mariadb:10.11
                                -->
                                <containerImage>postgres:15.2-alpine</containerImage>
                            </database>
                            <flyway>
                                <locations>
                                    filesystem:src/main/resources/db/migration/postgres,
                                    filesystem:src/main/resources/db/migration/postgresql
                                </locations>
                            </flyway>
                            <!-- 
                                You can configure any supporting jooq config here. 
                                see https://www.jooq.org/doc/latest/manual/code-generation/codegen-configuration/
                            -->
                            <generator>
                                <database>
                                    <includes>.*</includes>
                                    <excludes>flyway_schema_history</excludes>
                                    <inputSchema>public</inputSchema>
                                </database>
                                <target>
                                    <packageName>org.jooq.codegen.maven.example</packageName>
                                    <directory>target/generated-sources/jooq</directory>
                                </target>
                            </generator>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

Try with example application

```shell
$ cd examples/postgres-flyway-example
$ mvn clean package
```

The JOOQ code should be generated under example/target/generated-sources/jooq folder.

## CREDITS:
This plugin is heavily based on official https://github.com/jOOQ/jOOQ/tree/main/jOOQ-codegen-maven.
