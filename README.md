# spark
Spark Docker Image

Based on:

[catawiki](https://github.com/catawiki/)

[Singularities](https://github.com/SingularitiesCR/spark-docker)


## Example docker-compose.yml

```YAML
version: "2"

services:
  master:
    build: ./spark/ # location where the Dockerfile resides
    image: spark
    command: spark-class org.apache.spark.deploy.master.Master -h master
    hostname: master
    environment:
      SPARK_CONF_DIR: /conf
    ports:
      - 6066:6066
      - 7070:7070
      - 8080:8080
      - 50070:50070
    volumes:
      - ./conf/master:/conf
    volumes_from:
      - logfiles

  worker:
    build: ./spark/
    image: spark
    command: spark-class org.apache.spark.deploy.worker.Worker spark://master:7077
    environment:
      SPARK_WORKER_CORES: 1
      SPARK_WORKER_MEMORY: 2g
      SPARK_CONF_DIR: /conf
    links:
      - master
    volumes:
      - ./conf/worker:/conf
    volumes_from:
      - logfiles

  history:
    build: ./spark/
    image: spark
    command: spark-class org.apache.spark.deploy.history.HistoryServer
    hostname: history
    environment:
      SPARK_CONF_DIR: /conf
    expose:
      - 18080
    ports:
      - 18080:18080
    volumes:
      - ./conf/history:/conf
    volumes_from:
      - logfiles

  logfiles:
    image: alpine
    volumes:
      - /tmp/spark-events
```

### Start/stop/log containers

```sh
docker-compose up -d
docker-compose kill
docker-compose logs -f

```


### Run spark-shell

```sh
docker exec -it <container> spark-shell --master spark://master:7077

```

### Scaling

The number of service instances can be scaled up and down like below:

```sh
docker-compose scale worker=2

```

The workers will automatically register themselves with the master.
