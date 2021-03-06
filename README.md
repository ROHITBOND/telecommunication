# All resource list for all resource of this project
* slides, docs, etc, in shared google drive: https://drive.google.com/drive/folders/1RxiAF65TxsQQYNlToQf0eLN43lpVKUC6?usp=sharing
* slack communication team: https://5gopt.slack.com/


# telecommunication

+ python manage.py runserver

# file description in this project
| instructions_For_install_MongoCXXDriver.txt | how to install mongo cxx driver to implement communication between ns3 controller and mongoDB |
| media/ | folder for domination map which is dynamically generated in backend and updates when new documents add in mongo
| installShell.sh | script for automatically set up the development environment(not finish) |

## Create a new mongo user
+ db.createUser({user:"**", pwd:"******", roles:[{role:"userAdminAnyDatabase", db:"admin"}]})

## create a new database
+ use newDatabase

## import data into mongo database
+ mongoimport --db 5gopt --collection normal --type csv --file ~/code/data/normal_800m.csv --headerline --ignoreBlanks
+ mongoimport --db 5gopt --collection main_file_with_UserTHR --type csv --file ~/Downloads/main_file_with_UserTHR.csv --fields "Time,LocationX,LocationY,UserID,CellID,RSRP,RSRQ,SINR,UserThR" --ignoreBlanks
+ mongoexport --db 5gopt --collection normal --out traffic.json

## install mongo c driver and mongo cxx driver to implment communication between ns-allinone-3.26 and mongo v2.6.12
+ cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local -DCMAKE_PREFIX_PATH=/usr/local ..

## restore bson file into mongodb
* mongorestore -d db_name -c collection_name path/file.bson
* ./mongorestore -d 5gopt -c normal /home/tupevarj/Downloads/normal.bson
* ./mongorestore -d 5gopt -c controlpanel /home/tupevarj/Downloads/controlpanel.bson
* ./mongorestore -d 5gopt -c outage /home/tupevarj/Downloads/outage.bson
* ./mongorestore -d 5gopt -c event_log /home/tupevarj/Downloads/event_log.bson
* ./mongorestore -d 5gopt -c main_file_with_UserTHR /home/tupevarj/Downloads/main_file_with_UserTHR.bson

# development environment necessary libraries
| libraries | version | command to install |
| python    | 3.6.3   |  anaconda 3        |
| mongo     | 2.6.12  |  .........         |
| django    | 1.11.7  | [pip install Django==1.11.7] |
| MongoDB shell version | 2.6.12 | ..... |
| pymongo   | 3.5.1   | [pip install pymongo==3.1.1] |
| OS | RHEL 7 | ...... |
| pycharm | 2017.2.4 | ... |

| Database name | 5gopt |
| DB user name | ... |
| DB password | ... |

# related documentation for this project
* MongoDB C Driver 1.9.0 [http://mongoc.org/libmongoc/current/index.html]
* MongoDB C++ Driver manual [https://mongodb.github.io/mongo-cxx-driver/]
* PyMongo 3.6.0 Documentation [https://api.mongodb.com/python/current/]
* MongoEngine Documentation [http://docs.mongoengine.org/]

# Match between display figures in front end and database

| Display in Front End          | Data in MongoDB                     |
| ----------------------------- | ----------------------------------- |
| unnormal cell detection table | Fake data in Backend not from Mongo |
| Domination Map                | dominationmap                       |
| throughput line graph         | main_file_with_UserTHR              |

Collection schemas

| dominationmap |   x   |   y   |   z   |  sinr  |
| ------------- | ----- | ----- | ----- | ------ |
|     Demo      | -250  | -250  | 1.5   |  1.45  |

| main_file_with_UserTHR | Time | UserThR | LocationX | UserID | CellID | RSRP | RSRQ | SINR | LocationY |
| ---------------------- | ---- | ------- | --------- | ------ | ------ | ---- | ---- | ---- | --------- |
| Demo                   | 0.2  | NaN     |   948     |  1     | 1      | -113 | NaN  |  NaN |  1429     |

| event_log | "TIME" | "X" | "S" | "IMSI" | A2 RSRQ Enter | RSRQ   | CellID |
| --------- | ------ | --- | --- | ------ | ------------- | ------ | -------|
| Demo      |  8     | 964 | 423 | 19     |               | -9.315 |  14    |

| handover_log | "TIME" | "X" | "Y" | "IMSI" |       EVENT        | "CellID" |
| ------------ | ------ | --- | --- | ------ | ------------------ | -------- | 
| Demo         | 12.26  | 449 | 449 |20      | "Handover End OK"  |   11     | 

| normal(outage) | Time | UeNodeNo | UeRNTI | RSRP     | Serving_Cell | RSRQ | Cell_ID | 
| -------------- | ---- | -------- | ------ | -------- | ------------ | ---- | ------- |
| Demo           | 0,2  |       21 | 0      | -89,9397 | 0            | -nan |  20     |

| controlpanel | cellID | normal | outage | coc | cco | mro | mlb | dirty_flag |
| ------------ | ------ | ------ | ------ | --- | --- | --- | --- | ---------- |
| Demo         | 5      |     0  | 1      |  0  | 0   | 0   | 0   |    0       |




# run ns3 command
* ns-3.26>$ ./waf --run="CSONDemo --simTime=5 --ns3::LteHelper::HandoverAlgorithm=ns3::A3RsrpHandoverAlgorithm"


# to do list
1.  set up Apache when it's ready to deploy Django in production.
2.  preprocessing the data, which the final table schema should be like the following:

| Time | User | LocationX | LocationY | RSRP_1st | 1stRSRP_Corresponding_RSRQ| RSRP_2nd | 2ndRSRP_Corresponding_RSRQ | RSRP_3rd | 3rdRSRP_Corresponding_RSRQ | RSRP_4th | 4thRSRP_Corresponding_RSRQ| Serving Cell |
| ---- | --- | --------- | ---------- | -------- | ------------------------- | -------- | -------------------------- | -------- | ------------------------- | -------- | --------------------------- | ------------ |
| 0.2 | 1 | .. | .. | .. | .. | 
| 0.2 | 2 | .. |
| 0.2 | .. | .. |
| 0.4 | 1 | .. | .. |
| 0.4 | 2 | .. | .. |
Explaination
Serving Cell: pick the cell number which its rsrp value is the biggest, therefore, pick the rsrp_1st corresponding cell
calculate all users at specific time point, then for the next time point, repeat the above process. 
the final data schema should be following:




