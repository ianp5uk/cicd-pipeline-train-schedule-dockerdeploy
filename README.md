# cicd-pipeline-train-schedule-jenkins

This is a simple train schedule app written using nodejs. It is intended to be used as a sample application for a series of hands-on learning activities.

## Dependencies

The app has been built & proven to run with openjdk 11.0.6 & Gradle 6.2 on Ubuntu 19.10
The app itself uses node 9.11.1 & npm 5.6.0

## Build & run app standalone
    ./gradlew build
    ./gradlew npm_start
Once it is running, you can access it in a browser at [http://localhost:3000](http://localhost:3000)

## CI/CD
This repo & Jenkinsfile was built using Jenkins 2.204.2 & deploys to docker-ce v19.03.6.
The '-ce' installed with apt has proved important. Using the older Ubuntu snap version was error prone
