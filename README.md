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




