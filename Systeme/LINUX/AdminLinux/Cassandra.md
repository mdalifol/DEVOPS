# CASSANDRA

## GENERALITE




#### EXPORT ET IMPORT DE TABLE

1. Récupération nom keyspace applicatif
```bash
[admincas@BILICAS002 ~]$ keyspace=$(cqlsh -e "desc keyspaces;" -u cassandra | awk '{print $1 "\n" $2 "\n" $3 "\n" $4 "\n" $5 }' | while read line ; do if [[ $line == [x]* ]]; then echo $line ; else if [[ $line == [w]* ]]; then echo $line  ; fi; fi ; done)
echo $keyspace
xc9eac2b0_cfbf_45d5_bdf4_5f50c0d8a86e_group_3
```

2. Vérification table
```bash
[admincas@BILICAS002 ~]$ nodetool cfstats $keyspace.oauth_authorizations

[admincas@BILICAS002 ~]$ nodetool cfstats xc9eac2b0_cfbf_45d5_bdf4_5f50c0d8a86e_group_3.oauth_authorizations | grep 'Number of keys'
                Number of keys (estimate): 6376815
```


3. Export table
```bash
[admincas@BILICAS002 ~]$ cqlsh -e "copy $keyspace.oauth_authorizations to '/tmp/oauth_authorizations.dat';" -u cassandra -p xxxxxxxxx
```

4. Import table
```bash
[admincas@BILICAS002 ~]$ cqlsh -e "copy $keyspace.oauth_authorizations from '/tmp/oauth_authorizations.dat';" -u cassandra -p xxxxxxxxxxx

[admincas@BILICAS002 ~]$ cqlsh -u cassandra -p xxxxxxxxxxx
Connected to CAS_I_GROUP at BILICAS002.dom101.intres:9042.

[cqlsh 5.0.1 | Cassandra 2.2.8 | CQL spec 3.3.1 | Native protocol v4]
Use HELP for help.

cassandra@cqlsh> copy xc9eac2b0_cfbf_45d5_bdf4_5f50c0d8a86e_group_3.oauth_authorizations from '/save/webapi/cassandra/oauth_authorizations.dat' with SKIPROWS=10000;
Reading options from the command line: {'skiprows': '10000'}
Using 15 child processes

Starting copy of xc9eac2b0_cfbf_45d5_bdf4_5f50c0d8a86e_group_3.oauth_authorizations with columns [key, applicationID, created, id, scopes, subject].
Failed to import 20 rows: InvalidRequest - Error from server: code=2200 [Invalid query] message="Batch too large",  will retry later, attempt 1 of 5
Failed to import 20 rows: InvalidRequest - Error from server: code=2200 [Invalid query] message="Batch too large",  will retry later, attempt 2 of 5
Failed to import 20 rows: InvalidRequest - Error from server: code=2200 [Invalid query] message="Batch too large",  will retry later, attempt 3 of 5
Failed to import 20 rows: InvalidRequest - Error from server: code=2200 [Invalid query] message="Batch too large",  will retry later, attempt 4 of 5
Failed to import 20 rows: InvalidRequest - Error from server: code=2200 [Invalid query] message="Batch too large",  given up after 5 attempts
^C

cassandra@cqlsh> copy xc9eac2b0_cfbf_45d5_bdf4_5f50c0d8a86e_group_3.oauth_authorizations from '/save/webapi/cassandra/oauth_authorizations.dat' with CHUNKSIZE = 2;
Reading options from the command line: {'chunksize': '1'}
Using 15 child processes

Starting copy of xc9eac2b0_cfbf_45d5_bdf4_5f50c0d8a86e_group_3.oauth_authorizations with columns [key, applicationID, created, id, scopes, subject].
Processed: 6455970 rows; Rate:    3276 rows/s; Avg. rate:    8399 rows/s
6455970 rows imported from 1 files in 12 minutes and 48.669 seconds (0 skipped).
```
[chunksize explication](https://www.datastax.com/blog/six-parameters-tune-cqlsh-copy-performance)

5. Truncate table
```bash
keyspace=$(cqlsh -e "desc keyspaces;" -u cassandra | awk '{print $1 "\n" $2 "\n" $3 "\n" $4 "\n" $5 }' | while read line ; do if [[ $line == [x]* ]]; then echo $line ; else if [[ $line == [w]* ]]; then echo $line  ; fi; fi ; done)

cqlsh -e "truncate $keyspace.oauth_authorizations;" -u cassandra –p xxxxxxxx
```




#### FAIRE UN TRUNCATE
Le truncate est à utiliser en cas de

```bash
#en admincas :
cqlsh -e "truncate table system_distributed.parent_repair_history;" -u cassandra
cqlsh -e "truncate table system_distributed.repair_history;" -u cassandra
#à faire sur tous les noeuds du cluster
nodetool clearsnapshot
```
