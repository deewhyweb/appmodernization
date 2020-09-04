# appmodernization

Install the amq streams operator

`oc apply -f operators.yaml`

`oc apply -f operator-subscriptions.yaml`

`oc new-project daytrader`

## create kafka cluster with persistence

`oc create -f kafka.yml  -n daytrader`

## deploy kafdrop an opensource tool to view messages

`oc create -f kafdrop4.yml`

## deploy mysql abd debezium kafka connector

`oc new-app --name=mysql debezium/example-mysql:1.2 -n daytrader`

`oc set env dc/mysql MYSQL_ROOT_PASSWORD=debezium  MYSQL_USER=mysqluser MYSQL_PASSWORD=mysqlpw`

`oc  create secret generic my-sql-credentials --from-file=debezium-mysql-credentials.properties -n daytrader`

`oc apply -f kafka-connect.yml -n daytrader`

`oc apply -f mysqlconnector.yml -n daytrader`

Test the kafka topics

`oc  -n daytrader exec daytrader-cluster-kafka-0 -c kafka -i -t -- bin/kafka-topics.sh --bootstrap-server localhost:9092 --list`

## Deploy the daytrader app

Postgres:

`oc new-app -p POSTGRESQL_USER=tradedb -p POSTGRESQL_PASSWORD=tradedb -p POSTGRESQL_DATABASE=tradedb postgresql-persistent`

`oc delete limits daytrader-core-resource-limits`

`oc apply -f is-quarkus-trade-orders.yml`

`oc apply -f bc-quarkus-trade-orders.yml`

`oc apply -f dc-quarkus-trade-orders.yml`

## Day trader app

`oc new-app openliberty/open-liberty-s2i:latest~https://github.com/murphye/sample.daytrader8.git\#openshift`


