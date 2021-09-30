# system-design-notes


# Design principles 👎
1. Do requirement gathering - functional/NF
2. estimations 
3. Do HLD
4. Do Detailed design
5. Identify bottlenecks

tips :- 
use narrow tables 
use caching
partition database
usse indexes in db

for scale note users for famouse sites 
TW, GO, FB, instagram etc

Both horizontal scalability and redundancy are usually achieved via load balancing.

(This article won’t address vertical scalability, as it is usually an undesirable property for a large system, as there is inevitably a point where it becomes cheaper to add capacity in the form on additional machines rather than additional resources of one machine, and redundancy and vertical scaling can be at odds with one-another.)

Vertical scaling refers to adding more resources (CPU/RAM/DISK) to your server (database or application server is still remains one) as on demand.


Few LBs :- ELB , HAProxy , nginx 

Caching :- Memecache , Redis

BD caching is code free , invcalidation is difficult , Application level caching is code integrated , but invalidation is easier

Async systems 👎
Message queues :- SQS , RabbitMQ

periodic tasks :- 
EventHub , cron 

Map-reduce 
To optimize the SQl executions

dont store any persistent data on app servers 


Database : -
MySql with master -slave (read / write separately) , let it run with joins etc 
Do joins on application side  // preferred so that application becomes db independent ) 

Always do in memeory cache

use application side caching .


user sessions (never use the database!)
fully rendered blog articles
activity streams
user<->friend relationships 

Async :- 

keep results pre-populated
or
Do with help of Qs //better


Principals 👎
Availability
Performance
Reilability
Scalability
Manageability
Cost


Other components :- 
Queues
Load balancers
Indexes
Proxies
Distibuted caches
GLobal cache
Caches
Partitions
Redundancy
Services


# Indexing 

Indexes uses BTree , see other approaches

Search Index :-
three steps :- Crawlers , Indexing , Query 
Crawlers are distributed



feed  results to indexers 

![image](https://user-images.githubusercontent.com/4143476/134811683-92c01fcd-78a8-45aa-83ca-1839f0314cff.png)

Here lets say all words in english dictionary takes up 10,000 rows , but columns will take a lot of space
Another approach  is to use PQ , that can maintain Ranks as well , for each row . But that too is too much space given the fact that , in case there are 4 docs , many words might appear in them 

2 . use binary in doc columns such as 100 --> doc1 , not in doc2 , not in doc3
3. 
Better approach would be to have below structure some how 
![image](https://user-images.githubusercontent.com/4143476/134811860-64f22bfd-f5ed-433a-851b-e3aa64d9cd53.png)

Ebook apps searching 👎

word --> position1 , position2 , position2 ..


Query 👎
Conjuctive :- and operation , need all words in doc , does intersection of all words
Disjunctive :- or operation , does union of words result

Results by order :- 

![image](https://user-images.githubusercontent.com/4143476/134813955-05de2799-0b64-4d6e-b2e3-1cd374807b65.png)

here we want to check the order we could see results for each word and see if in a document , they occur in same order



# DropBox
feature :- upload/download/delete/update folder
sync folder 
history of updates to be maintained

scale :- 
10+ million users
100 million requests/day
high writes/reads 
version system also
My trial : -

![dropbox](https://user-images.githubusercontent.com/4143476/134819082-0df6fd27-3cbb-4209-bd82-242b2546729c.png)


How actual system is 👎

![image](https://user-images.githubusercontent.com/4143476/134819195-9aaad907-14ec-44cd-9a9f-3dbcfb2a766e.png)


# Cache designs

![Cachedesign drawio](https://user-images.githubusercontent.com/4143476/134822407-7bb04832-856e-4bd8-bc40-bfde58ffca41.png)

here we could use replication / master-slave (read write as well)

Consistent hashing looks like this 👎

take an array and store the hashesh of all nodes 

arr = [h1,h2,h3,h4,h5]

your requests should also generate the hashesh in same range

binary search hash of incoming request  in arr and fetch that node ,

addition of node no changes

one node is down , put all things from one to another


Distributed locks 👎

1. from a keystore , access the value 
2. generate  a new value 
3. while reading critical section , use key to get a value from store , if it matches current value , a lock can be obtained , put in your new value to block all others from obtaining locks 
4. if value doesn't match go back and fetch new value 


Stock exchange 👎
missed matching engine concept
separate Qs 

![stocvexchange drawio](https://user-images.githubusercontent.com/4143476/135311010-a3a175a5-8d42-4d36-a86a-c9131eb37dd1.png)



Tinder app design:-
![tinderdesign](https://user-images.githubusercontent.com/4143476/135311137-cf7771dd-0b60-4e12-a436-90064847fbfb.png)

tiktok
![tiktok drawio](https://user-images.githubusercontent.com/4143476/135321595-24c9d09f-97fd-4da6-a097-087b5f6d2e99.png)

Data estimation 👎
type of db- Relational or NoSql
amount of data in database --> 100 million customers 
10% on cache -> ideal size is 2 GB , then shard
Query per second 
concurrent requests :-  

Is system read heavy / write heavt 


Resilience :- Circuit breakers 
Highly available --> replication , cache , CDN , Redis , 99.99 means few mins per week , that means microservices deployment
consistency :- 2 phase commits , sagas , outbox pattern 
WAF 
ELK -
Autoscaling :- if traffic is cyclical use predictive autoscaling
talk between two services using service registry 
configs in secrets
ELB is example of service registry
