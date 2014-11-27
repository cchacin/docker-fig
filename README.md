HOWTO
=======

Docker + boot2docker + fig

# Installation

```bash
brew install docker boot2docker fig
```

# Create a Dockerfile

```Dockerfile
FROM dockerfile/java:oracle-java7

RUN apt-get update && \
    apt-get install -yq --no-install-recommends wget pwgen ca-certificates && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ENV CATALINA_HOME /var/lib/tomee

RUN mkdir ${CATALINA_HOME} && \
     wget -qO- "http://apache.tradebit.com/pub/tomee/tomee-1.7.1/apache-tomee-1.7.1-plus.tar.gz" \
     | tar -zx --strip-components=1 -C ${CATALINA_HOME}

ADD target/*.war ${CATALINA_HOME}/webapps/myapp.war

EXPOSE 8080

CMD ${CATALINA_HOME}/bin/catalina.sh run
```

# Create a fig.yml file

```yml
my_app_server:
  build: .
  ports:
     - "8080:8080"
  links:
   - my_db_server
   - my_redis_server

my_db_server:
  image: mysql
  ports:
    - "3306:3306"

my_redis_server:
  image: redis
  ports:
    - "6379: 6379"
```

# Start boot2docker

```bash
boot2docker up
```

# Set the environment variables that'll configure Docker and Fig to talk to it

```bash
$(boot2docker shellinit)
```

# Build image and Start fig

```bash
fig build
```

```bash
fig up
```

# To use your application:

```bash
$(boot2docker ip)
```

Output:

```
The VM's Host only interface IP address is:
192.168.59.103
```

Go to http://192.168.59.103:8080/myapp in your browser
