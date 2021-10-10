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


sticky session
rate limits - fixed , sliding window ,
Leaking bucket :-  all drops can be exhausted in short duration and rest of the time we sit still
implmn :- keep updating and reducing buckets with each requests and then refresj the bucket at start  
sliding log :-  store like below
10th sec - 1
9th sec - 1
7th sec -1
.
.

here sum all for past 1 min

sliding window :- store like fixed window . Fixed window has a problem that it in last 1 second we got 100 requests , and next 1 sec (start of new windows) we got another 100 , we would get burst of traffic

to avoid this , look at current window and see how much window we have exausted . and add the logs from previous window , For example :- if we are at 25% in our window , we will see 75% logs from previous window . If it exeeds threshhold , we stop requests

use cache to store the keys  , for APIs use consumer keys 


Cache 👎
Write-through cache: written to cache while writing to db

cache aside :- application reads from cache and then hit db . read heavy systems are benefitted from this . 
To avoid inconsistencies , we could implement TTL 

with Cache-control Cache-Control: Max-Age and age :- these values are for browser side caching

Read through cache :- like cache aside policy , but cache always stays consistent with Db (responsibilty of cache lib)

write back cache :- cache to DB sync happens at a later stage :- write heavy systems



pre loading cache to avoid db load when cache is booted

Invalidation strategy :--
1. FIFO :- First added cache is evicted first
2. LIFo :- removes most recently added data
3. LRU :- least recently used
4. LFU :- least frequently used
5. Random Selection :- random 

AWS has options vlatile LRU :- takes TTL only LRU , all LRU :- takes all LRU

Load balancing techniques :- 
Random selection
Round Robin :- simple
Weighted round robim :- processing powers with round robin
Least connection :- 
Least response time
Source IP Hashing :- sticky session

Scalability 👎
DB partition 


Availability / Reliability
Replication


Circcuit breaker :- 
```
interface ICircuitBreakerStateStore
{
  CircuitBreakerStateEnum State { get; }

  Exception LastException { get; }

  DateTime LastStateChangedDateUtc { get; }

  void Trip(Exception ex);

  void Reset();

  void HalfOpen();

  bool IsClosed { get; }
}
```

https://docs.microsoft.com/en-us/azure/architecture/patterns/

ambassador pattern - 
out of process but in same hosting env . if using container , then host it separately 
can provide common functionality like monitoring , logging , CBreaking etc

![image](https://user-images.githubusercontent.com/4143476/136404464-5e99410f-1fe6-4304-8e11-5720889933f9.png)

Anti corruption layer 👎
used to map two services speaking different formts or technologies 
![image](https://user-images.githubusercontent.com/4143476/136404732-819c6468-28ce-420e-b12e-487c99a3068d.png)

Asynchronous Request-Reply pattern
In most cases, APIs for a client application are designed to respond quickly, on the order of 100 ms or less. 
Polling is useful to client-side code, as it can be hard to provide call-back endpoints or use long running connections. Even when callbacks are possible, the extra libraries and services that are required can sometimes add too much extra complexity.
![image](https://user-images.githubusercontent.com/4143476/136405846-e006100b-2d22-47b7-82c8-70be4026d55b.png)

![image](https://user-images.githubusercontent.com/4143476/136406061-d3cb1eef-7037-4fe0-b648-7a4a335d8a51.png)


Backedns for Frontends 
Create separate backend services to be consumed by specific frontend applications or interfaces. This pattern is useful when you want to avoid customizing a single backend for multiple interfaces.
![image](https://user-images.githubusercontent.com/4143476/136406691-9e19289b-a737-459d-b824-7146ceaf31be.png)

Bulkhead
Keep services isolated
![image](https://user-images.githubusercontent.com/4143476/136408876-ac2f083b-1c41-4984-b00b-60db4dace30b.png)
keep separation based on connection pool , failure of one service does not affect other
![image](https://user-images.githubusercontent.com/4143476/136408957-653961a1-16e6-4868-a167-4fd862083527.png)

Cache Aside 
Lifetime of cached data.  TTL
Evicting data.
Priming the cache.
Consistency.



One of the drives for eventual consistency is that distributed data stores are subject to the CAP Theorem. This theorem states that a distributed system can implement only two of the three features (Consistency, Availability, and Partition Tolerance) at any one time. In practice, this means that you can either:

Provide a consistent view of distributed (partitioned) data at the cost of blocking access to that data while any inconsistencies are resolved. This may take an indeterminate time, especially in systems that exhibit a high degree of latency or if a network failure causes loss of connectivity to one or more partitions.
Provide immediate access to the data at the risk of it being inconsistent across sites. Traditional database management systems focus on providing strong consistency, whereas cloud-based solutions that utilize partitioned data stores are typically motivated by ensuring higher availability, and are therefore more oriented towards eventual consistency.


Consistency 👎
Try to achieve enevtual consistency 
Hightly consistent solution , will need to acquire lock and hence system will not be highly available
CQRS with Event sourcing is way to go forward
take care of caches in case of eventual consistency


CDN 👎
video stream , static content , firmware updates etc
challanges :---
caching
versioning
cache-control
testing 
content security
resilience

Choreography 
in orchestrator pattern , if we add a new service some rewiring is needed . We can use choreograpgy pattern where each service talk to other using a queue
![image](https://user-images.githubusercontent.com/4143476/136423272-1e9da40f-a0bc-42b9-9bf3-a1dee8580505.png)

Use the choreography pattern if you expect to update, remove, or add new services frequently. The entire app can be modified with lesser effort and minimal disruption to existing services.

Circuit breaker 
concurrency 
testing 
monitoring
Recoverability --> when to test from open to half closed can be time bound or can also be # of failed request bound


Claim Check 👎
![image](https://user-images.githubusercontent.com/4143476/136424467-16da8a2e-e052-4332-8798-b8740632a2ad.png)

in case of large messages , dont directly put it on bus , instead store it in db and send a reference to that message
The pattern can also be used if the payload should be accessed only by services that are authorized to see it. By offloading the payload to an external resource, stricter authentication and authorization rules can be put in place, to ensure that security is enforced when sensitive data is stored in the payload.

CQRS
different data schemes for rea dand write
Independent scaling
Optimized data schemas
Security
Separation of concerns
Simpler queries

Compensating Transaction pattern

![image](https://user-images.githubusercontent.com/4143476/136435673-36c97aa0-de3d-4b7a-b120-44f165a0dc9c.png)

Undo the work performed by a series of steps, which together define an eventually consistent operation, if one or more of the steps fail. Operations that follow the eventual consistency model are commonly found in cloud-hosted applications that implement complex business processes and workflows.
keep things idempotenet so that compensating transaction also succeed


Competing consumers
![image](https://user-images.githubusercontent.com/4143476/136438844-6010f34d-a34a-4175-a7e2-abb29b813438.png)
lambda pattern


deployment-stamp: 
![image](https://user-images.githubusercontent.com/4143476/136439555-c6fdc3b7-389b-483c-ad08-562e28672258.png)

event-sourcing
store the data changes as a set of events in a event store and consumers are decoupled from the events
example :- 
![image](https://user-images.githubusercontent.com/4143476/136440416-3618e4a2-4710-4fb8-be84-0ec53e1ba974.png)


external configuration store
![image](https://user-images.githubusercontent.com/4143476/136440547-9d0ca31e-c60e-4e85-8fed-2612cbaf2882.png)


Federated Identity pattern
![image](https://user-images.githubusercontent.com/4143476/136441197-11c9e8e3-5f3c-4b62-a71a-70e3596fb352.png)

Gatekeeper
![image](https://user-images.githubusercontent.com/4143476/136441276-e21cee85-a22d-4e21-82dc-1a282afc524b.png)


gateway agrregator
![image](https://user-images.githubusercontent.com/4143476/136441351-78881366-bffb-45af-bbb8-59b4003d6ab1.png)


Gatteway offloading
Offload some features into a gateway, particularly cross-cutting concerns such as certificate management, authentication, SSL termination, monitoring, protocol translation, or throttling.

![image](https://user-images.githubusercontent.com/4143476/136441503-ef0d964d-13c9-443b-bce4-769c8b9294e2.png)

Gateway Routing Pattern
use L7 information from request and route to correct service
![image](https://user-images.githubusercontent.com/4143476/136442482-4ede055e-7d2d-441e-9991-8397a13670a2.png)


Geode pattern
The Geode pattern involves deploying a collection of backend services into a set of geographical nodes, each of which can service any request for any client in any region.
![image](https://user-images.githubusercontent.com/4143476/136442558-97449b96-2ed3-4586-9cb1-22152b76e036.png)

Health Endpoint Monitoring pattern
![image](https://user-images.githubusercontent.com/4143476/136442745-1e56eb5e-433c-466a-aaea-2fa7e11f6ba0.png)

Index  table

Leader election
In a cloud-based system that implements horizontal scaling, multiple instances of the same task could be running at the same time with each instance serving a different user. If these instances write to a shared resource, it's necessary to coordinate their actions to prevent each instance from overwriting the changes made by the others.

Materialized views
views that can remain updated

Queue based load levelling
use q to balance the load

retry pattern


good resource
https://docs.microsoft.com/en-us/dotnet/architecture/cloud-native/relational-vs-nosql-data

2ps commits :- commit request, acquire lock and then commit
sagas can be used with event sourcing and compensating transactionn


Scheduler agent supervisor
adds resiliency  👎
![image](https://user-images.githubusercontent.com/4143476/136706087-97988369-7246-4a38-a352-74277302f851.png)




