# jpt
java performance tuning

1) GOF - 23 patterns classified into 
	1. Creational - Singleton, Factory, Builder, Prototype, Abstract Factory
	2. Structural - Adapter, Proxy, Facade
	3. Behaviour - Template, Strategy
Classification of GOF patterns
	1. Class scope (Inheritance - 4)
	2. Object scope (Composition - 19)

2) JavaEE patterns
	1. Presentation tier - front controller
	2. Business Tier - session facade, business delegate
	3. Data Tier - DAO

3) Architectural patterns

	MVC, MOM (message oriented module), SOA, REST, Micro services

4) EIP

	Splitter, Aggregator, wiretap, CBR (content based routing)

5) Micro Services

	circuit breaker
	
Performance Tuning:
SMART

Specific
Measurable
Attainable
Relevant
Time-related
	
"high throughput low latency"

Anti pattern - magic servlet

POJO is independent of API, should be portable

Container is pre-written application, it provides 
	1. life cycle management
	2. declarative services (transactions, security, caching etc)
	3. distributed environment/remoting

7 transaction attributes in spring, 6 transaction attributes are there in EJB
ORM - Hibernate, Kodo, Toplink, MyBatis

GC Algorithm
1. Serial GC	(high throughput)
2. Parallel GC	(high throughput) (default for JVM 1.7 and 1.8)
3. CMS Concurrent Mark & Sweep GC 	(for low latency)
4. G1 GC							(for low latency) (default JVM 1.9)


To know the default GC
java -XX:+PrintCommandLineFlags
jps
jcmd    - to know the list of JVM processes
jstack -l <pid> 	- to know the stack information
jmap -dump:file=heapDump-2.jmap 4944 	- to generate the heap dump
jhat heapDump-2.jmap	- to view heap dump



GC will only identify the live objects through GC ROOTS (Threads, Static Variables, JNI, etc) for garbage collection


Method Area (PermGen/MetaSpace) will store .class files, XML meta-data etc.,
Stack Aria - -XSS2M (up to 2 MB)
XMS or XMX - allocation of memory from RAM/Virtual Memory
-XX:+PreTouch - allocation of memory from RAM
Native Memory Tracking: -XX:NativeMemoryTracking=[off | summary | detail]
this is available jdk 1.8

Parallel GC
Young 	--> parallel new GC
Old 	--> parallel old GC

CMS/G1
Young 	--> Parallel new GC
Old 	--> concurrent and parallel

Senior GC always run one thread
when more than one Java process is running then we can XXXXX

System.gc() will always triggers FullGC (Heap and MetaSpace together) this is not recommended in production 

-XX:DisableExplicitGC - will be used disable the System.gc(), it is recommended in production.

Full GC --> Young, Old and MetaSpace

CMS -->Initial mark, concurrent mark, remark, concurrent sweeping
GC Route --> Young --> Old (object reference)

In CMS stop the world will happen in -- initial mark and remark 
From Card table we will get reference objects
Objects are marked as null will be removed during Minor GC
drawback of CMS --> it is not compact


G1 

G1 stands for Garbage-First
Region size is fixed, if any object comes with a bigger size then couple of regions will be formed into humongous regions and the bigger objects will be shared.
All the young collection and some of old collection is called mixed.
mixed collection --> all young and few old
Eden --> Survival --> old 
mixed collection is stop the world.
G1 can be used for higher heap sizes.



java.util.concurrency api
Locks
1.reentrant - similar to synchronized block but maintains fairness (longest waiting thread will get priority)
2.readwritelock
3.stampedlock
4.stampedlock with optimistic read



performance tuning:
auto-boxing, collections, synchronization/locking, 

Isolation levels:

Read committed	
Repeatable read	
Snapshot		
Serializable 	(Pessimistic)


JMX

1. Distribution layer	- JConsole (Management Application)
2. Agent Layer			- JMX Kernel (MBeanServer-Registry of MBeans)
3. Instrumentation layer- Which contains MBeans

MBean should be suffixed with MBean (for ex: HeapManagementMBean)

Example MBeans - Data Source, Threads, Logging etc

There are four types of MBeans
1. Standard
2. Dynamic
3. Model
4. Open

This can be achieved in Spring using @ManagedAttribute, @ManagedOperation, @ManagedResource


Types of references

1. Strong References (for ex: A a = new A())
2. Soft References (for ex: SoftReference<A> softA = new SoftReference<A>(a); a = null; a = softA.get(); )
	normally used for Object Pool
3. Weak References (for ex: WeakReference<A> weakA = new WeakReference<A>(a); a = null; a = weakA.get(); )
	normally used for MetaData, ReadOnly Caching
4. Phantom References 


Spring Prototype scope beans will participate only in the construction life-cycle not in the destroy life-cycle, @preDestroy is ignored

use this VM argument to know the duplicate strings.
-XX:+UseG1GC -XX:+UseStringDeduplication -XX:+PrintStringDeduplicationStatistics


					Standard Deployment Descriptor		Tomcat DD
Tomcat (Globale conf)web.xml							context.xml
WAR (local conf)	web.xml								context.xml



Need to explore:
dynamic module (for class loading)
OSGI Bundles (for JBOSS server level class loading)
WeakHashMap
@ManagedAttribute, @ManagedOperation, @ManagedResource
jitwatch-master


