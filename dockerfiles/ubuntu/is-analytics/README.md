# Dockerfiles for WSO2 Identity Server Analytics #

This section defines the step-by-step instructions to build [Ubuntu](https://hub.docker.com/_/ubuntu/) Linux based Docker images for multiple profiles
provided by WSO2 Identity Analytics 5.7.0, namely:<br>

1. Dashboard
2. Worker

## Prerequisites

* [Docker](https://www.docker.com/get-docker) v17.09.0 or above

## How to build an image and run
##### 1. Checkout this repository into your local machine using the following git command.
```
git clone https://github.com/wso2/docker-is.git
```

>The local copy of the `dockerfile/ubuntu/is-analytics` directory will be referred to as `ANALYTICS_DOCKERFILE_HOME` from this point onwards.

##### 2. Add JDK, WSO2 API Manager Analytics distributions and MySQL Connector to `<ANALYTICS_DOCKERFILE_HOME>/files`.

- Download [JDK v1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
and extract it to `<ANALYTICS_DOCKERFILE_HOME>/base/files`.
- Download the [WSO2 Identity Server Analytics 5.7.0](https://wso2.com/identity-and-access-management/install/analytics/)
and extract it to `<ANALYTICS_DOCKERFILE_HOME>/base/files`.
- Download [MySQL Connector JAR v5.1.45](https://downloads.mysql.com/archives/c-j) and copy that to `<ANALYTICS_DOCKERFILE_HOME>/base/files` folder <br>
- Once all of these are in place, it should look as follows:

  ```bash
  <ANALYTICS_DOCKERFILE_HOME>/base/files/jdk<version>/
  <ANALYTICS_DOCKERFILE_HOME>/base/files/mysql-connector-java-5.1.45-bin.jar
  <ANALYTICS_DOCKERFILE_HOME>/base/files/wso2am-analytics-5.7.0/
  ```

>Please refer to [WSO2 Update Manager documentation](https://docs.wso2.com/display/WUM300/WSO2+Update+Manager)
in order to obtain latest bug fixes and updates for the product.

##### 3. Build the base Docker image.

- For base, navigate to `<ANALYTICS_DOCKERFILE_HOME>/base` directory. <br>
  Execute `docker build` command as shown below.
    + `docker build -t wso2is-analytics-base:5.7.0 .`
    
##### 4. Build Docker images specific to each profile.

- For dashboard, navigate to `<ANALYTICS_DOCKERFILE_HOME>/dashboard` directory. <br>
  Execute `docker build` command as shown below.
    + `docker build -t wso2is-analytics-dashboard:5.7.0 .`
- For worker, navigate to `<ANALYTICS_DOCKERFILE_HOME>/worker` directory. <br>
  Execute `docker build` command as shown below.
    + `docker build -t wso2is-analytics-worker:5.7.0 .`
    
##### 5. Running Docker images specific to each profile.

- For dashboard,
    + `docker run -p 9643:9643 wso2is-analytics-dashboard:5.7.0`
- For worker,
    + `docker run -p 9090:9090 -p 9091:9091 wso2is-analytics-worker:5.7.0`
    
##### 6. Accessing the Dashboard portal.

- For dashboard,
    + `https:<DOCKER_HOST>:9643/portal`
    
>In here, <DOCKER_HOST> refers to hostname or IP of the host machine on top of which containers are spawned.

## How to update configurations
Configurations would lie on the Docker host machine and they can be volume mounted to the container. <br>
As an example, steps required to change the port offset using `deployment.yaml` is as follows.

##### 1. Stop the Identity Server Analytics container if it's already running.
In WSO2 Identity Server Analytics 5.7.0 product distribution, `deployment.yaml` configuration file <br>
can be found at `<DISTRIBUTION_HOME>/conf/worker`. Copy the file to some suitable location of the host machine, <br>
referred to as `<SOURCE_CONFIGS>/deployment.yaml` and change the offset value under ports to 2.

##### 2. Grant read permission to `other` users for `<SOURCE_CONFIGS>/deployment.yaml`
```
chmod o+r <SOURCE_CONFIGS>/deployment.yaml
```

##### 3. Run the image by mounting the file to container as follows.
```
docker run 
-p 7713:7713
--volume <SOURCE_CONFIGS>/deployment.yaml:<TARGET_CONFIGS>/deployment.yaml
wso2is-analytics-worker:5.7.0
```

>In here, <TARGET_CONFIGS> refers to /home/wso2carbon/wso2is-analytics-5.7.0/conf/worker folder of the container.


## Docker command usage references

* [Docker build command reference](https://docs.docker.com/engine/reference/commandline/build/)
* [Docker run command reference](https://docs.docker.com/engine/reference/run/)
* [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
