## Iniciative
The initial idea is to create a repository where I can place different experiments related streaming libraries. The idea is to start with Akka Streams and then compare with Spark Streaming and other libraries.

The main goal is to reproduce a production environment in your local machine. For doing that, what it is better than Docker?

In this example it is define:
1. Kafka Broker using the spotify kafka image. Has been evaluated as well the [wurstmeister/kafka](https://hub.docker.com/r/wurstmeister/kafka/), but it works better this one.
2. A producer and consumer using the [Akka Streams Kafka](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) library.
3. An example how to run Spark locally using docker
    - Create a spark cluster: spark master and spark workers using docker compose.
    - Create a consumer using spark streaming.
      - To create the consumer I tried to use the scala template available here
            lhttps://github.com/big-data-europe/docker-spark
      - The template was not up to date with the latest version of spark, so I created a new template available as part of this build. See the [spark-docker-template](https://github.com/dvirgiln/streams-kafka/tree/master/spark-docker-template) subproject.
## Initial Requisites
* Have sbt installed
* Have docker and docker-compose installed.

## Goal
* Create kafka infrastructure using docker
* Create spark cluster infrastructure using docker.
* Create one simple producer and consumer that write and consume random numbers.
* Explore Akka Streams and Spark Streaming.
* Usage of docker-compose and swarm

## Instructions usage using docker-compose
    1. sbt docker
    2. docker swarm init
    3. docker stack deploy -c docker-compose.yml streams
    4. docker service ls
    5. docker logs -f $akka_producer_container
    6. docker logs -f $spark_consumer_container
    7. docker logs -f $akka_consumer_container
    8. http://localhost:8080 to access the spark cluster console.
    8. docker stack rm streams
    9. docker swarm leave --force


## Running consumer and producer outside of docker
    1. Modify the docker-compose.yml and the producer main and consumer main to point to localhost:9092
    2. Run the kafka-spotify image alone: using docker run, or commenting the code in the yml file related the consumer and producer

## Cluster monitoring
Use kafka-manager to monitor kafka. Very useful.

        https://github.com/yahoo/kafka-manager

Start localhost:9000 in the browser.

## Technical considerations
  * Usage of docker in one click:
    * Kafka broker
    * Spark cluster. It is possible to see the workers and jobs submitted in http://localhost:8080
    * Akka producer.
    * Akka consumer.
    * Spark streaming consumer.
  * Creation of a scala template that allows to submit spark jobs to the spark master node.
  * All the projects are dockerized using sbt. Has been used the [marcuslonnberg plugin](https://github.com/marcuslonnberg/sbt-docker)
  * This is a good example how to run different subprojects using  different scala versions:
             [sbt-cross plugin](https://github.com/marcuslonnberg/sbt-docker)
  * Example how to use docker stack to scale some services (spark-workers)

## Install kubernetes on Mac
    1. brew cask install minikube
    2. Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
    3. minikube start

## Local Kubernetes

    1. minikube delete
    2. rm -rf ~/.minikube
    3. minikube start
    4. minikube dashboard

Useful commands:

* kubectl create -f 1_kafka_broker.yml
* kubectl delete pod,service kafka-broker
* kubectl get services
* kubectl get deployments
* kubectl get pods    
* //This creates a service
* kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 80800
* echo $(minikube ip)
* kubectl logs kubernetes-bootcamp-5c69669756-mts2z
* kubectl delete pod,service,deployment -l app=spark-master
* kubectl describe service kafka-broker-svc
* kubectl exec -it kafka-broker-5d68b98857-l6bjw -- /bin/bash

## Notes kubernetes
* To create a service with a external ip address define the type of the container as LoadBalancer, but minikube doesnt support the LoadBalancer type.
## Useful links:
Usage of spotify docker image:

        https://github.com/spotify/docker-kafka

How to build multiprojects with different scala versions:

        https://github.com/lucidsoftware/sbt-cross

Spark Docker images from:
        https://github.com/big-data-europe/docker-spark

Kafka vs Flume:
        https://www.linkedin.com/pulse/flume-kafka-real-time-event-processing-lan-jiang/

Kubernetes:

            https://kubernetes.io/docs/user-journeys/users/application-developer/foundational/

            https://github.com/kubernetes/minikube

            https://www.virtualbox.org/wiki/Downloads

    Interesting how kubectl creates the environment variables for the service, so another service
    can use this service.
            https://kubernetes.io/docs/concepts/services-networking/service/#environment-variables

    I had problems to read from local docker images with minikube:
            https://stackoverflow.com/questions/38979231/imagepullbackoff-local-repository-with-minikube

Machine Learning:
            http://www.disfrutalasmatematicas.com/datos/desviacion-estandar.html
            https://machinelearningmastery.com/supervised-and-unsupervised-machine-learning-algorithms/
            https://github.com/apache/spark/blob/dcdda19785a272969fb1e3ec18382403aaad6c91/examples/src/main/scala/org/apache/spark/examples/mllib/StreamingLinearRegressionExample.scala
