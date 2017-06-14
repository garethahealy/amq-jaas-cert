[![Build Status](https://travis-ci.org/garethahealy/amq-jaas-cert.svg?branch=master)](https://travis-ci.org/garethahealy/amq-jaas-cert)
[![License](https://img.shields.io/hexpm/l/plug.svg?maxAge=2592000)]()

# amq-jaas-cert
OpenShift PoC for JBoss AMQ 6.2.1 using: [JaasDualAuthenticationPlugin](https://github.com/garethahealy/amq6-dual-jaas-plugin)

## Upstream docs
- https://github.com/jboss-openshift/application-templates/blob/master/docs/amq/amq62-ssl.adoc

## Deploy to OCP
create templates

    oc create -f https://raw.githubusercontent.com/jboss-openshift/application-templates/master/amq/amq62-ssl.json -n openshift
    oc create -f https://raw.githubusercontent.com/jboss-openshift/application-templates/master/jboss-image-streams.json -n openshift

create SA

    oc create serviceaccount amq-service-account
    oc policy add-role-to-user view system:serviceaccount:$(oc project -q):amq-service-account

create secret

    oc create secret generic amq-app-secret \
        --from-file=amq-broker.ks=generated-certs/amq-broker.ks \
        --from-file=amq-broker.ts=generated-certs/amq-broker.ts

todo: create bc
    
    todo

create template

    oc process amq62-ssl \
        -p AMQ_KEYSTORE=amq-broker.ks \
        -p AMQ_KEYSTORE_PASSWORD=password \
        -p AMQ_TRUSTSTORE=amq-broker.ts \
        -p AMQ_TRUSTSTORE_PASSWORD=password \
        -n openshift | oc create -f -

## Send test message 
todo

    $AMQ_HOME/bin/activemq-admin \
        -Djavax.net.ssl.trustStore=/tmp/src/amq-client.ts \
        -Djavax.net.ssl.trustStorePassword=password \
        -Djavax.net.ssl.keyStore=/tmp/src/amq-client.ks \
        -Djavax.net.ssl.keyStorePassword=password producer \
        --brokerUrl ssl://localhost:61617 \
        --user userDKV \
        --password l4Kkg8Pp \
        --destination queue://MY.TEST.QUEUE \
        --messageCount 1 \
        --message HelloWorld 
        
todo

    $AMQ_HOME/bin/activemq-admin \
        -Djavax.net.ssl.trustStore=/tmp/src/amq-client.ts \
        -Djavax.net.ssl.trustStorePassword=password \
        -Djavax.net.ssl.keyStore=/tmp/src/amq-client.ks \
        -Djavax.net.ssl.keyStorePassword=password consumer \
        --brokerUrl ssl://localhost:61617 \
        --user userDKV \
        --password l4Kkg8Pp \
        --destination queue://MY.TEST.QUEUE \
        --messageCount 1

todo

    mvn activemq-perf:consumer \
        -Djavax.net.ssl.trustStore=/Users/garethah/Documents/github/garethahealy/amq-jaas-cert/amq-client.ts \
        -Djavax.net.ssl.trustStorePassword=password \
        -Djavax.net.ssl.keyStore=/Users/garethah/Documents/github/garethahealy/amq-jaas-cert/amq-client.ks \
        -Djavax.net.ssl.keyStorePassword=password \
        -Dfactory.brokerURL=ssl://192.168.64.2:30789 \
        -Dconsumer.destName=queue://MY.TEST.QUEUE \
        -Dconsumer.recvType=count \
        -Dconsumer.recvCount=1 
