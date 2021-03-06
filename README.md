# appmodernization

Install the amq streams operator

`oc apply -f operator-subscriptions.yaml`

## create kafka cluster with persistence

`oc new-project kafka`

`oc create -f kafka.yml  -n kafka`

## deploy kafdrop an opensource tool to view messages

`oc create -f kafdrop4.yml`


Test the kafka topics

`oc  -n kafka exec demo-cluster-kafka-0 -c kafka -i -t -- bin/kafka-topics.sh --bootstrap-server localhost:9092 --list`

## Deploy the daytrader app

`oc new-project daytrader`

`oc new-app --name=mysql debezium/example-mysql:1.2 -n daytrader`

`oc set env dc/mysql MYSQL_ROOT_PASSWORD=debezium  MYSQL_USER=mysqluser MYSQL_PASSWORD=mysqlpw`

`oc  create secret generic my-sql-credentials --from-file=debezium-mysql-credentials.properties -n kafka`


`oc apply -f kafka-connect.yml -n kafka`

`oc apply -f mysqlconnector.yml -n kafka`

Postgres:

`oc new-app -p POSTGRESQL_USER=tradedb -p POSTGRESQL_PASSWORD=tradedb -p POSTGRESQL_DATABASE=tradedb postgresql-persistent`

`oc delete limits daytrader-core-resource-limits`

`oc apply -f is-quarkus-trade-orders.yml`

`oc apply -f bc-quarkus-trade-orders.yml`

`oc apply -f dc-quarkus-trade-orders.yml`

## Day trader app

`oc new-app openliberty/open-liberty-s2i:latest~https://github.com/murphye/sample.daytrader8.git\#openshift`

## CRW

`oc new-project image-puller`

`oc apply -f image-puller-operator-group.yml`

`oc apply -f image-puller-operator.yml`

`oc apply -f image-puller.yml`

`oc new-project code-ready-workspaces`

` oc apply -f crw-operator-group.yml`

`oc apply -f crw-operator.yml`

`oc apply -f crw-cluster.yml`