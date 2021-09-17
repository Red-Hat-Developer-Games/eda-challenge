# EDA with Quarkus and Apache Kafka Challenge

## Introduction

The main goal will be for you to deploy 🚀 the foundational components of an Event-Driven Architecture on
top of Red Hat OpenShift Container Platform. The foundational components of
this Event-Driven Architecture should be:

- An Apache Kafka cluster with some topics to publish and consume messages.
- A Quarkus application to publish and consume messages from topics of Apache Kafka cluster
- An Service Registry to publish the Data schema formats or API of the messages of our solution
- OpenShift Operators to operate some of the components of our platform: Apache Kafka and Service Registry

## Apache Kafka ➕ OpenShift = Strimzi Operators

Apache Kafka on OpenShift in a few minutes 💪.

[Apache Kafka](https://kafka.apache.org) is an open-sourced distributed event streaming platform
for high-performance data pipelines, streaming analytics, data integration, and mission-critical applications.

[Strimzi](https://strimzi.io/ provides a way to run an Apache Kafka cluster on OpenShift in
various deployment configurations.

This powerful operator is available (already deployed and ready) in your OpenShift cluster. Use it! 😉

These references are very useful for you:

* [Strimzi QuickStarts](https://strimzi.io/quickstarts/)
* [Using Strimzi](https://strimzi.io/docs/operators/latest/using.html)

## Service Registry ➕ OpenShift = Apicurio Operators

Service Registry on OpenShift in a few minutes 💪.

[Service Registry](https://www.apicur.io/registry/) is a datastore for sharing standard event schemas
and API designs across API and Event-Driven Architectures. You can use Service Registry to decouple
the structure of your data from your client applications, and to share and manage your data types
and API descriptions at runtime using a REST interface.

This powerful operator is available (already deployed and ready) in your OpenShift cluster. Use it! 😉

These references are very useful for you:

* [Apicurio Registry Documentation](https://www.apicur.io/registry/docs/apicurio-registry/2.0.1.Final/index.html)

## Quarkus Application

The main goal here is to create a Quarkus application to expose a simple REST API to publish messages into a
Kafka topic, and consume them to be processed. If you have never used Quarkus before, remember that you
can attend the Quarkus workshop before the Games. The only requirement is Java basic development knowledge.

Quarkus provides a [reactive messaging extension](https://smallrye.io/smallrye-reactive-messaging/smallrye-reactive-messaging/3.9/index.html)
to publish and consume messages easily from Kafka clusters. It will be the core of your application.

Throughout this challenge we propose to use the following message structure to use:

```java
package com.santander.games.challenges.eda;

import java.io.Serializable;

public class Message implements Serializable {

    // Key to identify this message
    private String key;

    // Value of the message
    private String value;

    public String getKey() {
        return key;
    }

    public void setKey(String key) {
        this.key = key;
    }

    public String getVaue() {
        return value;
    }

    public void setValue(String value) {
        this.value = value;
    }

}
```

You have to provide a service exposing the following endpoints over REST:

- An endpoint to publish a new message into the Kafka topic. Suggested path to access
this endpoint (using POST verb): `/topic`, `/publish`
- An endpoint returning all the messages stored from the topic. Suggested path to access
this endpoint (using GET verb): `/topic`, `/consume`

You can of course add other endpoints if you want.

You are encouraged to use [reactive messaging extension](https://smallrye.io/smallrye-reactive-messaging/smallrye-reactive-messaging/3.9/index.html)
to manage the publish and subscribe workflows.

These references will help you to develop this challenge:

* [Quarkus Apache Kafka Reference](https://quarkus.io/guides/kafka)
* [Using Apache Kafka with Schema Registry and Avro](https://quarkus.io/guides/kafka-schema-registry-avro)

**Bonus (Optional)**: Using [Avro](https://avro.apache.org/) schemas to define API specifications for your
events in an Apache Kafka cluster is considered a best practice in Event-Driven Architectures. If your
application integrates this amazing feature, you already have built a robust solution, and we will
consider it as an extra mile to win.

We propose the following AVRO schema:

```json
{
  "name": "Message",
  "namespace": "com.santander.games.challenges.eda",
  "type": "record",
  "doc": "Schema for a Message.",
  "fields": [
    {
      "name": "key",
      "type": "string",
      "doc": "Key to identify this message."
    },
    {
      "name": "value",
      "type": "string",
      "doc": "Value of the message."
    }
  ]
}
```

### Bootstrapping the project

First, you need a new project. The easiest way to create a new Quarkus project is to use the
[Quarkus Code Generator](https://code.quarkus.io/) 🏃 adding the extensions you need.

Other alternative is to open a terminal and run the following command:

```shell
mvn io.quarkus.platform:quarkus-maven-plugin:2.2.3.Final:create \
    -DprojectGroupId=org.acme \
    -DprojectArtifactId=eda-challenge \
    -DclassName="com.santander.games.challenges.eda.GreetingResource" \
    -Dpath="/hello"
```

This command allows to bootstrap a maven based application and creates a JAX-RS endpoint.
**NOTE**: check the last Quarkus version available in the [site](https://quarkus.io/)

When testing or running in dev mode you should use the Kafka and Apicurio services provided
out of the box by DevServices from Quarkus. Check how it works [here](https://quarkus.io/guides/kafka-dev-services)
and [here](https://quarkus.io/guides/apicurio-registry-dev-services).

For prod mode you should have deployed the Apache Kafka and Service Registry services in your
OpenShift cluster. You will need to configure the Quarkus Application to connect such services.

Now you should be ready to run the bootstrapped application. The dev mode is an awesome mode that
Quarkus has that allows you to write code and get the result of your changes without having to
restart the application at all.

To start the application in `dev` mode use: `./mvnw compile quarkus:dev`.

This will also listen for a debugger on port 5005. If you want to wait for the debugger to attach
before running you can pass `-Dsuspend` on the command line. If you don’t want the debugger
at all you can use `-Ddebug=false`.

Once started, you can request the provided endpoint: 

```shell
curl -w "\n" http://localhost:8080/hello
```

Now you are set!

**Bonus (Optional)**: If your application allows users to publish messages and to consume them with
some kind of API or UI, it will be much appreciated for us.

### Packaging and run the application

The application is packaged using `./mvnw package`. Check the information in the `README.md` file
that will help you with some basic commands.

### Containerization and deployment in OpenShift

Next part of the challenge, once the Quarkus Application is coded and is accepting requests
locally, you should package it and deploy it to the provided OpenShift Cluster. For that, we
recommend using the [Quarkus OpenShift extension](https://quarkus.io/guides/deploying-to-openshift). This
extension offers the ability to generate OpenShift resources. Once the resources have been
generated, you can deploy them by running the command `oc apply -f path_to_file`.

If you are not familiar with the OpenShift client, the `oc` command, pass the `quarkus.kubernetes.deploy`
flag in the command line to build and deploy the application in a single step.

**Bonus (Optional)**: Quarkus is the unique Kubernetes Native Java stack for containers! Really? Demostraste
it by building your application as a native application and deploy in the OpenShift cluster.

Check the questions:
- How long does it take to start up your application?
- How much memory does your application need to run?

Check how to do it [here](https://quarkus.io/guides/building-native-image)
