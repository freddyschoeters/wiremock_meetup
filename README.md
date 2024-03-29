# Wiremock workshop
This repo is used for a Wiremock workshop to build up experiences through exercises.  
For the section of possible exercises, have a look at Exercises.md in the root of this repo.

It also can be used as an endpoint for API test frameworks.

The standalone version can be started locally directly via the java command, as well as via docker (Dockerfile is provided to build the image).
The Jenkins pipeline is also foreseen.

Wiremock can be used as a dependency and also as a standalone process and this repo focusses only on the standalone version.

## Clone repo

```bash
$ git clone git@github.com:sts-tobania/wiremock.git
```

## Local installation

Wiremock can be started via local java, but also a custom docker image can be built.
Both ways are explained below.

### Download Wiremock
2 versions are availble: 2.x.x and 3.x.x  
v2 is compatible up until Java 8. v3 requires minimum Java 11

Download can be found here
Best is to take the standalone version 2.35.00 as this one is compatible with Java 8.  
As from version 3.0.0 Java 11 is required.

The current latest version is available via [this direct link](https://repo1.maven.org/maven2/org/wiremock/wiremock-standalone/3.3.1/wiremock-standalone-3.3.1.jar) 

### Running Wiremock locally
Wiremock is a simple jar, so the main command to start is
```bash
$ java -jar wiremock-standalone-3.3.1.jar
```
Obviously there are lots of possible parameters you can provide. The whole list can be found [here](https://wiremock.org/docs/standalone/java-jar/#command-line-options).  
Parameters we often use are:  
\--port : Provide the port to run on  
\--verbose : print the logs to the console  
\--global-response-templating : return input values  
\--jetty-header-buffer-size 16384 : Increase the header buffer size (needed at BNPPF to handle saml)  
\--record-mappings : If you want to record your traffic (request/response pairs)  
\--root-dir : the path to your working directory

## Docker usage

### Base Image

The base image is got from [Docker Hub](https://hub.docker.com/r/wiremock/wiremock)

### Build custom wiremock image including the mocks

```bash
$ docker build -t wiremock-example .
```

### Run wiremock locally via docker

```bash
$ docker run -it --rm --name wiremock-example -d -p 9002:8080 wiremock-example
```

You can access the API to fetch all pets at http://localhost:9002/users

If you want to access a specific user we have 10 users defined.  
To retreive the information of a specific user, access the API like http://localhost:9002/users/1

For a POST request, use POST http://localhost:9002/users (body doesn't matter)

## Structure of a Wiremock project
Wiremock will require 2 directories:
* __files : Used to place your response files
* mappings : the "rules" Wiremock will map on

If you start Wiremock with the basic command above, it will use the directories from the location of the jar. If the directories are not found, it will create them.  
As I work with multiple stub projects (which are also differently structured), I have the habbit to place my Wiremock jar in a central location and use --root-dir to the exact place of working directories ( __files/ and mappings/ )

Free tip of the day: as every command starts with
```bash
$ java -jar <location_of_your_jar>
```
and it can be tricky with the absolute paths for both Wiremock as your stub project, (as a Mac user) I have created an alias:
```
alias wiremock="java -jar /Users/freddy/workspace/dev-home/wiremock-jre8-standalone-2.30.0.jar"
```

With having this alias, I easily run Wiremock as:
```
wiremock --root-dir ./stubs --verbose --global-response-templating --port 8081
```

## Run wiremock on Jenkins
On Jenkins a job can be configured using the Jenkinsfile provided.  
Make sure to rebuild the job once there are stubs changed or added

## Example responses
To see if Wiremock has mappings, you can use `http://localhost:8080/__admin/` which will return all configured mock.

The following endpoints are available:
* http://localhost:8080/users --> returns all user
* http://localhost:8080/users/1 --> returns user 1
* http://localhost:8080/users/99 --> User not found error
* http://localhost:8080/users/one --> Bad request

Some POST endpoints are also abvailable, ie:
POST http://localhost:8080/users
with body
```
{
    "id": 2,
    "name": "Leanne Graham",
    "username": "Bret",
    "email": "Sincere@april.biz",
    "address": {
        "street": "Kulas Light",
        "suite": "Apt. 556",
        "city": "Gwenborough",
        "zipcode": "92998-3874",
        "geo": {
            "lat": "-37.3159",
            "lng": "81.1496"
        }
    },
    "phone": "123456789",
    "website": "hildegard.org",
    "company": {
        "name": "Romaguera-Crona",
        "catchPhrase": "Multi-layered client-server neural-net",
        "bs": "harness real-time e-markets"
    }
}
```