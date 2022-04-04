# How to manually push docker image to ECR

Building docker container is facilitated using Google's JIB maven plugin. To dockerize project, add the following in the in the `build->plugins` section of the `pom.xml` file:

```
 <plugin>
        <groupId>com.google.cloud.tools</groupId>
        <artifactId>jib-maven-plugin</artifactId>
        <version>3.2.0</version>
        <configuration>
          <from>
            <image>amazoncorretto:17</image>
          </from>
          <to>
            <image>${dockerRepository}/${project.artifactId}:${project.version}</image>
          </to>
          <allowInsecureRegistries>true</allowInsecureRegistries>
          <container>
           <creationTime>USE_CURRENT_TIMESTAMP</creationTime>
          </container>
        </configuration>
      </plugin>
```

Secondly add the following in the 'properties' part (at the beginning of `pom.xml`):

```
<dockerRepository>364952602419.dkr.ecr.eu-central-1.amazonaws.com</dockerRepository>
```


To build project agains local docker installation run the following:

```
mvn jib:dockerBuild
```

To test whether docker container is running as expected run it with:

```
docker run -p <port>:<port> <image>
```

, where `<port>` is port where your microservice runs.

To verify everythng is ok, open `http://localhost:<port>/swagger` in your browser. 


Once everything is fine, local image can be pushed to ECR by issuing the following command: `mvn jib:build`



On the first attempt you might get `403 Forbidden` response. Please try the following:

```
aws ecr get-login-password --region eu-central-1 | docker login --username AWS --password-stdin 364952602419.dkr.ecr.eu-central-1.amazonaws.com
```

Expected output is `Login Succeeded`. After that run `mvn jib:build` again. Expected output is: `[INFO] BUILD SUCCESS`.

If `jib:build` again throws `403` please consult one of the AWS admins to check whether repository for your project is created in ECR (`364952602419.dkr.ecr.eu-central-1.amazonaws.com/<service_name>`).

