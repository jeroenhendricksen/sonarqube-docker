# Quality & Assurance using SonarQube

## About

This project allows to run a [sonarqube](https://www.sonarqube.org/) server locally with the help of docker and docker-compose, so you can get an idea of your code quality before sending any analysis to a shared SonarQube instance.

The SonarQube Community Edition suppports analysis of a single branch for a repository.

## Prerequisites

- docker
- docker-compose 1.24 (or newer)
- a Java or Kotlin project that uses maven as build-tool
- port `9000` must be free on your machine (you can change this in the `docker-compose.yml`)

## Usage

1. First start SonarQube locally

    1. Start the containers, run `docker-compose up -d` from this folder.
    1. Wait a minute or so.
    1. Visit your local sonarqube instance: [http://localhost:9000](http://localhost:9000).
    1. Login with `admin`/`admin`.

1. Run the analysis for a *maven* project

    1. Go to the project root (where the project' root `pom.xml` file resides).
    1. Run sonar analysis: `mvn sonar:sonar -Dsonar.host.url=http://localhost:9000`
    1. Now analyze the results via the SonarQube Web UI: [http://localhost:9000](http://localhost:9000).

1. Run the analysis for a *gradle* project

    1. Go to the project root (where the project' root `build.gradle` file resides).
    1. Ensure your `build.gradle` file was set-up for sonarqube. Read more at [SonarQube for gradle](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-gradle/) and/or look at this [example build.gradle with SonarQube support](https://github.com/SonarSource/sonar-scanning-examples/blob/master/sonarqube-scanner-gradle/build.gradle).
        1. I had to add these blocks to my `build.gradle` file:

            ```gradle
            plugins {
                id "org.sonarqube" version "2.7"
            }
            ```

            and (optionally)

            ```gradle
            sonarqube {
                properties {
                    property 'sonar.projectName', 'PROJECTNAME'
                }
            }
            ```

    1. Run sonar analysis: `gradle -Dsonar.host.url=http://localhost:9000 -d sonarqube`. `-d` is added for debugging the SonarQube analysis performed. Any sonarqube related messages should contain the string `org.sonarqube.gradle`, so you can filter the output using `grep`.
    1. Now analyze the results via the SonarQube Web UI: [http://localhost:9000](http://localhost:9000).

## Known issues

When you see sonarqube output this error in the logs...

`max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]`

...you should increase the `vm.max_map_count`:

```bash
sysctl -w vm.max_map_count=262144
```

## IntelliJ integration using sonarlint

1. Generate an API key in SonarQube for the admin user.
1. Install the sonarlint plugin in IntelliJ.
1. Configure the sonarlint plugin. Point it to localhost:9000 and provide the api key.
1. Open a file and the sonarlint tab. It shows all the findings, per row.

## Cleanup

Stop SonarQube and postgres with `docker-compose down`. This will cleanup the sonarqube and postgres containers, but not the data volumes. To cleanup all the data volumes as well, you can execute `docker-compose down -v`.
