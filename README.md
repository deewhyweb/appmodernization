# appmodernization

#Steps to deploy kafka cluster, kakfa connect cluster, mysql , mysql debezium cdc connector, kafdrop
# Note: the amq streams operator should have been installed as a prereq 
# login to oc
oc login  
#new project
oc new project daytrader

oc create project daytrader
#create kafka cluster with persistence
oc create -f kafka.yml  -n daytrader
# wait for a few and ensure the kafka pods are running
 oc get pods
# deploy kafdrop an opensource tool to view messages
oc create -f kafdrop4.yaml
# create the kafka connect cluster
oc create -f kafka-connect-s2i.yml
# wait for a few and ensure the kafka connect cluster pods are running
 oc get pods
# deploy mysql
oc new-app --name=mysql debezium/example-mysql:1.2 -n daytrader

oc set env dc/mysql MYSQL_ROOT_PASSWORD=debezium  MYSQL_USER=mysqluser MYSQL_PASSWORD=mysqlpw
# s2i build with mysql drivers
  oc start-build morgan4-connect-cluster-connect --from-dir ./kafka-connect-s2i -n daytrader
  # you might run into imagestream /unauthorized issues ..the following addresses that
  Note: create your secret from access.redhat.com and save file
oc create -f <your secret file>.yaml -n daytrader
oc get secrets
#find the secret just created for the command below
 oc secrets link morgan4-connect-cluster-connect <your pull secret name>
#rebuild the kafka connect cluster
oc start-build morgan4-connect-cluster-connect --from-dir ./kafka-connect-s2i -n daytrader
#Deploy mysql connector
oc create -f mysqlconnector.yml -n daytrader
