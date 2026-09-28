Vid1 Notes ( https://www.youtube.com/watch?v=bwt09KXDH94 )
- Jordan is alpha🐺

Types of storage:
- HDD/SDD - lasts until broken 
- RAM - Temporary 
Wanna keep data, use HDD

When it comes to storing data, you would not use a computer (application servers) for data storage
- no one should host data on a VM in form of some regular files 
--> too much going on with the OS, resource management
--> bad for requests from multiple clients (betas)
--> no separation of consern between data storage and app server 
--> ass for scalability

Clients requests to the application servers are a typical bottle neck, so if you add on some data handling to it, it will suck even more

We use a Data Base (DB) --> Dedicated Computer for storage --> stores all the needed data for the application (but one DB is not enough)

--> Reason for this whole video series
==============================================================================================================================================

Vid 2 Notes: Internals of a DB   (https://www.youtube.com/watch?v=LzNdvuj3a5M)
- Jordan wears two tshirts in this video and he is a shy pooper

Important stuff from vid before:  
1. Data should be stored persistently  
2. Data should be stored on a hard drive and not in RAM (Jordan enter your jokes here)  
3. Data is stored in HDD  

New stuff:  
- Data allocation (position on the hard drive) defines the reading speed  
- Data stored on an array: key-value type of storage  
When you want to find some info with the key/word "Jordan" you need to run through the whole table to get the item  
--> O(n) time complexity, same for writing (find --> write)

Idea for writing:  
instead of overwriting the older field we add the changed key value/item to the bottom of the table  
and make read operations from the bottom, since we keep the pointer (read it up) on the bottom of the disc  
--> O(1) for writing --> nice ✅  
--> O(n) readtime, but table more rows, so more of the n --> not so good in real cases ❌

Most of the time we care more for reading speed  
--> we use indexes 📚
--> finding a row by a key
--> make a range query: show rows with names between A-B

Indexes:
for better read speed you pay with writing speed --> still a win
--> for other info check next vid


O(n) --> time complexity depends on the amount of items in the table/array  
10 items with db reading speed of 1 sec will get you the 10th item in 10 seconds (worst case) ⏱


==============================================================================================================================================

Vid 3 Notes: Hash indexes (https://www.youtube.com/watch?v=I1wQsY-Nh_k )

- Jordan defines gravity

Why we need indexes (notes from previous videos):

1. Improve the reading speed (better than O(n))
2. We have to go through the whole table to get our value (thats bad)

Hash indexes (are based on hash maps)
--> use hash function (abstract method)
--> takes key (memory location) and returns a value back (is O(1) for reads and writes)

Two keys may produce the same hash index. That’s called a collision
It can be handled with 
- chaining (create a list)
- probing (find next possible slot)

Point of index easily scan for certain key

Jordan—> place on disc O(1) for writing or reading 

Issues:

- hashmaps are bad on disc —> all hash indexes (keys) are kept on memory
- Ram is expensive—> less data can be stored
- Ram is not durable
- No range querees

Solution

- write ahead log (WAL) —> before writting to the hashmap change is first written on HDD
- crash happens—> we replay the logs
1. on restart the db checks the log
2. commited but not applied operations are red
3. operations are re-applied to the in-memory hashmap
4. the system resumes


==============================================================================================================================================
Vid 4 Notes B-Tree Indexes: (https://www.youtube.com/watch?v=Z2OaqmxiH20 )
- Jordan has no haters, only male lovers

B-Trees:

- Type of DB indexes
- kept on the disk
- has a tree-like structure
- to find a road traversing through multiple nodes/references should happen
- Trees should be balanced (like a real tree)

Writting new elements to Trees

- if there is space, we can add new element —> we change the referance
- if no space —> child node should be splitted —> if not enough nodes should be splitted till the root node to keep the tree balanced —> check some videos on balancing the trees

B-Tree dies?

- tree is inconsistent
- write ahead log would be helpful (or sequential only write log)
- have a copy tree

Summary vs. Hash

- selfbalancing tree on disk
- Slower but still uses some RAM for caching the upper levels of tree, buffering page reads/writes/write ahead logging
- Good reads
- Not all keys need to fit in memory —> no limits to data set size
- range queries are possible

Thx for the vid Jordan <3

==============================================================================================================================================

Vid 5 Notes B-Tree Indexes: (https://www.youtube.com/watch?v=ciGAVER_erw)

LSM Tree Indexes

- Another type of DB indexes developed some time after B-Trees
- In memory balanced binary search tree (Red Black, AVL, B-tree) —> Writes are O(log n)
- since in memory (RAM) —> no durability—> write ahead logs (to refolmulate the LSM Tree) —> still fast writes
- also less space (as in other indexes, since we need to store all in RAM) —> use space from disk when tree too big
1. Reset the tree (purge of all nodes)
2. convert LSM tree to SSTable on disk: sorted in order with all the scores kept the same (immutable) —> expensive? no, we use in-order traversial algorithm O(n)

In-order Traversal

def inorder(root):

  if root is not None:

    inorder(root.left)  # traverse the left subtree

    print(root)  # visit + print current node

    inorder(root.right)  # traverse the right subtree

  2
 / \
1   3


The output would be 1, 2, 3 (ask some ai for step-by-step execution)

*How SSTables are used for reads? O(log n)*

1. Look for a key in LSM Tree
2. if not found —> check the most recent SSTable + second most recent —> if the key is the same 👍 O(log) since binary search

*How to delete a key? (Tombstone)*

Deleted key gets marked in most recent Table

*LSM-Tree optimizations*

- Sparse index —> keys locations from the SSTable are extracted and written on disk (faster binary search (does not take a lot space on disk)
- Bloom Filter (to be continued)
    - if key is present in a table —> could be wrong
    - if key not present —> definitely not present

*LSM main issue + solution:*

- wasted storage: no real deletion of keys (tombstone), update —> adding a key again
- table compaction: merges SSTables in background and takes the more recent value  —> takes computing power 👎
- merging two sorted lists —> O(n)

Summary:

- *used in Cassandra**, **LevelDB**, **RocksDB**, **HBase*
- great for write heavy workloads
- Just check the next vid or notes by *@SreekantShenoy*


Indexing techniques:
1) Hash Index 
- They are basic hashmaps in memory. (NOT disk - as we need random hashmap access instantly and not seek it)
- Thus limited by memory space, expensive
- Fast reads and writes
- Bad for range queries
- To add durability, we add WAL - write to disk as log (easy as pointer is always at the end) and then write to the hashmap

2) B-Trees
	- Self balancing tree, entirely kept on disk
	- Hence NO limit on size
	- Advantage having lesser concern over durability
	- Slower reads than “Hash index” technique as - disk vs memory speed.
	- Reads are fast - O(LogN) to retrieve, but slow writes
	- Good for range queries (as stored in tree structure)
	- Durability can be ensured by NOT updating tree in place, rather updating the pages/block separately file and then updating the reference later on.

3) LSM Tree + SSTable
	- In-memory tree table (AVL, red-black), self balanced
- Little faster writes compared to B-Trees, but slower than hash index
- Slower reads speeds than B-Trees, as it has to check all SStables (Also slower range queries)
- Durability handled by WAL - which can be replayed later
	- Space concern of LSM trees as in-memory, However we have SStables which are outputted to disk
	- Hence No of keys limit not a concern (to store by space)
	- SSTable - sorted, immutable
	- Finding key - can go on taking time - it is first checked in tree (memtable) and only then across SSTable backwards from creation time
	- To delete, its implemented by tombstone value - indicating deleted. It is added to the latest SSTable entry

LSM Optimizations
Use Sparse index 
				- Also known as index for SSTable
- Take “certain” keys of SSTable and create a sparse index with corresponding stored memory location.
	- Can use binary search to find in-between keys
Bloom Filters
				- Can vaguely tell, if certain “key” present in the SSTable
Compaction
				- Merges SSTables in background - O(N) operation
				- Cons is extra CPU processing in background could be bad
				
				
---------------------------------------------------------------------------------------------------------------------------------------------------------

Vid 6 : Index Concluded : https://www.youtube.com/watch?v=QO7KTO-8RWM


https://www.youtube.com/watch?v=QO7KTO-8RWM

Indexes : Faster reads on specific key value slower writes for any possible write.

Index Abstraction : Sorted by key -> O(log n)


A clustered index means the actual table data is physically organized according to the index key.
Think of it like a dictionary 📖:
  A → Apple
B → Banana
C → Cat
D → Dog

The data itself is arranged in the same order as the index.

Simple example

Suppose employees are stored by empId:

10 → Amit
20 → Rahul
30 → Kumar
40 → Priya
50 → Ravi


If empId is the clustered index, the table's data storage is organized around that ordering.

So a query like:

SELECT *
FROM employee
WHERE empId BETWEEN 20 AND 40;

can efficiently access the relevant range.


| Clustered index                                  | Non-clustered index                   |
| ------------------------------------------------ | ------------------------------------- |
| Data is organized according to the index         | Index is separate from the table data |
| Usually one clustered ordering per table         | Multiple indexes possible             |
| Excellent for range access on the clustering key | Good for targeted lookups             |



Important PostgreSQL point

Since you're working with PostgreSQL:

PostgreSQL does not have a traditional always-maintained clustered index like SQL Server.

PostgreSQL has the CLUSTER command, which physically reorganizes a table according to an existing index, but that ordering is not automatically maintained as rows are inserted/updated.


So if you're learning this concept generally, understand:

Clustered index
      ↓
Index order ≈ physical table-data order

And don't assume CREATE INDEX in PostgreSQL creates a clustered index.



If you're using PostgreSQL, you don't create a traditional clustered index with CREATE INDEX.

Instead, you first create a normal index:

CREATE INDEX idx_employee_empid
ON employee(empId);

Then physically organize the table according to that index:

CLUSTER employee
USING idx_employee_empid;


So,

CREATE INDEX
     ↓
Create the index
     ↓
CLUSTER employee USING idx_employee_empid
     ↓
Reorganize table according to that index


select * from pg_indexes

SELECT
    indexname,
    indexdef
FROM pg_indexes
WHERE tablename = 'Employee';

indexname              | indexdef
-----------------------|--------------------------------------------
Employee_deptId_idx    | CREATE INDEX ... ON public."Employee" USING btree ("deptId")


Multi Dimensional Index  - If we just need to organise the data 

A composite index (also called a multi-column index) is an index created on more than one column.

Simple example

Suppose you have:

Employee
--------------------------------
id | deptId | salary | name
1  | 10     | 50000  | Amit
2  | 10     | 80000  | Rahul
3  | 20     | 60000  | Priya
4  | 20     | 90000  | Kumar

Suppose your common query is:

SELECT *
FROM employee
WHERE deptId = 10
AND salary > 60000;


You could create a composite index:

CREATE INDEX idx_employee_dept_salary
ON employee(deptId, salary);

Now the index is built using both deptId and salary.

Why is this useful?

Instead of having two separate indexes:

deptId index
salary index

you have one index organized roughly like:

deptId     salary
------------------
10         50000
10         80000
20         60000
20         90000

So PostgreSQL can efficiently narrow down:

deptId = 10
      ↓
then salary > 60000
      ↓
Rahul

Order matters

This is very important.


CREATE INDEX idx
ON employee(deptId, salary);

is not the same as:

CREATE INDEX idx
ON employee(salary, deptId);

----------------------------------------------------------------------------------------------------------------------

Vid 7 : Introduced to ACID Database Transaction 
 : https://www.youtube.com/watch?v=oGmxzUBCYtY
 
 
 ACID is a set of properties that make database transactions reliable and safe.
 
 | Letter | Meaning     | Simple meaning                                              |
| ------ | ----------- | ----------------------------------------------------------- |
| **A**  | Atomicity   | **All or nothing**                                          |
| **C**  | Consistency | Data remains **valid/correct**                              |
| **I**  | Isolation   | Transactions don't improperly **interfere** with each other |
| **D**  | Durability  | Once committed, data **stays saved**                        |


Simple bank example

Suppose you transfer ₹1,000 from A → B.

A: ₹5,000 → ₹4,000
B: ₹2,000 → ₹3,000

Atomicity:
Both operations happen, or neither happens. You don't want ₹1,000 to disappear.

Consistency:
The database rules remain valid after the transaction.

Isolation:
If another transaction is happening at the same time, it shouldn't see an incorrect intermediate state.

Durability:
Once the transfer is committed, a server restart shouldn't make the transfer disappear.

🧠 Easy way to remember

A = All or nothing
C = Correct state
I = Independent transactions
D = Data stays

PostgreSQL is a transactional relational database designed around these ACID properties.


Atomicity:
A transaction is all or nothing - if any part of the transaction fails, the whole transaction (when transferring money from one account to another - either both the debit and credit happen or neither does)

Consistency:
A transaction brings the database from one valid state to another - no invariants (after a transaction, no foreign key constraints are violated)

Isolation:
Concurrent transactions do not interfere with each other. No race conditions (two people buy a concert seat and they are not both getting the same one)

Durability:
Once a transaction is committed, it's permanently saved even if the system crashes (after a purchase confirmation, the record stays even after a power outage)

==========================================================================================================

Vid 8 Notes: Read Commited Isolation --https://www.youtube.com/watch?v=oS60pr8H1e0

- i googled Corrina Kopf and as google says she earned more than 50 Mil USD with OF, sometimes I really question why tf i need to have a carreer. life is unfair...

Read Commited Isolation : A transaction can only read data that another transaction has already committed.

Why is it called "Read Committed"?

Because when you read, you only see data that has been committed.

anyways

*Side note to DBs:*
- Databases are multithreaded
- no way to know the order of execution

Commit
- commit a write —> happens after all writes to all rows of the table are done

** Race conditions types **
Dirty write: [Dirty write happens when one transaction overwrites data written by another transaction before the first transaction has committed.]
- same row is overwritten and commited by two different operations(writing over the commited values)
- Solution —> locking of the row which is in use —> dead lock is an issue —> find a solution (like random timer)

Dead lock is the situatuion which used to fix the dirty write.

Dirty reads: [Dirty read = reading data that another transaction changed but hasn't committed yet.]
- a uncommited write is read (fail of write or race condition) —> invalid read
- using locks for reads would be very expensive —> as we remember reads are more important then writtes
- Solution —> store the old value of a row while there is an incoming write and then the database can return the old value until the incoming write is committed

Database are multithreaded, so bunch of write thread and read thread are going on and there is no way to know the exact order, which actually cretae a race condition.

Commit means a one operation either read or write is done and moved in the database


Dirty read = reading uncommitted data
Dirty write = writing/overwriting uncommitted data

Dirty Write : The main way to prevent dirty writes is to make the database coordinate concurrent transactions so two transactions cannot overwrite the same data incorrectly.

Simple example

Suppose:

salary = ₹50,000

Transaction A:

UPDATE employee
SET salary = 60000
WHERE id = 101;

Before A commits, Transaction B tries:

UPDATE employee
SET salary = 70000
WHERE id = 101;

The database can lock the row for A's update:
-------------------------
Transaction A
    ↓
Locks employee 101 🔒
    ↓
UPDATE salary = 60000
    ↓
COMMIT
    ↓
Unlock 🔓
-------------------------

In PostgreSQL

PostgreSQL's normal row-level locking/concurrency control prevents this kind of dirty write. You can also explicitly lock rows when your business logic requires it:

SELECT *
FROM employee
WHERE id = 101
FOR UPDATE;

FOR UPDATE essentially says:

"I'm going to modify this row; don't let another transaction modify it at the same time."

🧠 Remember

Dirty Write
     ↓
Two transactions modify same data concurrently
     ↓
Database concurrency control / row locking
     ↓
Prevents conflicting uncommitted writes

And this is different from dirty read: isolation levels such as Read Committed prevent a transaction from reading another transaction's uncommitted changes.


Besides row locking, there are a few approaches, depending on your application.

1. Use transactions

Put related operations inside one transaction:

BEGIN;

UPDATE employee
SET salary = 60000
WHERE id = 101;

COMMIT;

If something goes wrong:

ROLLBACK;

The database handles the concurrent transaction safely.

2. Row-level locking

As we discussed:

SELECT *
FROM employee
WHERE id = 101
FOR UPDATE;

This is useful when you need to read a row and then make a decision/update based on it.

3. Optimistic locking

Instead of locking the row immediately, keep a version column:

id | salary | version
101 | 50000 | 5

Application A reads version 5.

Then updates:

UPDATE employee
SET salary = 60000,
    version = 6
WHERE id = 101
  AND version = 5;
  
  
  Dirty Write
    ↓
1. Transactions
2. Row locking (FOR UPDATE)
3. Optimistic locking (version column)
----------------------------------------------------------------------------------------------------------------------------------------

Vid 9 Notes: Snapshot isolation (another race condition)  https://www.youtube.com/watch?v=Tgpa9TrxsfU

Situation:

- Total balance across accounts must always be 1 million $
- Balance is transferred between accounts only (A → B → C → D).
- A long read transaction checks balances in sequence
- Meanwhile, another transaction transfers money from E to A
- Result: the reader sees inconsistent data (e.g., A has new balance, others are old) → 100,000 $ discrepancy

Cause:

- Under snapshot isolation, each transaction sees a snapshot of the DB at start time
- Updates by other transactions during the read are not visible, leading to partial states being read

Solution: write ahead log

- Every write is first logged → ensures durability and atomicity
- Each transaction gets a monotonic ID
- Data is versioned: values are stored along with their transaction ID
- Readers see only data committed before their transaction started → consistent snapshot

In few words: we read snapshots instead of db table for reads


Snapshot Isolation is a database transaction isolation level where a transaction sees a consistent snapshot of the database as it existed when the transaction started.
Simple example

Suppose we have:

Account A = ₹10,000

Transaction T1 starts and reads A:

T1 → reads ₹10,000

Meanwhile, transaction T2 updates A:
T2 → changes A to ₹8,000 → COMMIT

Because T1 is using snapshot isolation, if T1 reads A again, it can still see:
T1 → ₹10,000

It sees the snapshot from its transaction start, rather than T2's newly committed value.

Why is it useful?

It allows transactions to read data without being blocked by other transactions writing data.

A simplified comparison:

| Isolation              | What can happen?                                   |
| ---------------------- | -------------------------------------------------- |
| Read Uncommitted       | Can see uncommitted changes                        |
| Read Committed         | Sees committed data, but repeated reads can differ |
| **Snapshot Isolation** | Consistent snapshot throughout transaction         |
| Serializable           | Transactions behave as if executed one at a time   |


Important point

Snapshot isolation usually uses MVCC (Multi-Version Concurrency Control).

Instead of overwriting the old value immediately, the database maintains different versions:

A = ₹10,000  ← version visible to T1
A = ₹8,000   ← newer version created by T2

So T1 can continue reading the older version while T2 works with the newer one.

Snapshot isolation allows a transaction to read a consistent snapshot of committed data from a particular point in time, while other transactions can concurrently modify the database.


Drawback :

Snapshot isolation prevents many read/write conflicts, but it doesn't guarantee full serializability.

--------------------------------------------------------------------------------------------------------------------------

https://www.youtube.com/watch?v=eym48yrObhY


Vid 10 Notes: Write Skew and Phantom writes (another race condition)

- 10/ 60, i guess ill be done with everything this year

Notes:

Write Skew 1

- locks are useful to prevent race condition (as we know)
- but what happens if the objects are not impacted by eachothers locks? (during write skew)
- we grab only locks of single objects and not both
Solution: we need to grab the locks, where a defined condition is met

Write Skew 2 (Phantom Writes)

- two transactions execute concurrently and attempt to insert new rows that conflict with each other
- since the conflicting data doesn’t exist yet, no locks are held—leading to a phantom problem
- the transactions do not see each other’s uncommitted changes and believe their inserts are valid
- as a result, both insert new rows that, together, violate a constraint (e.g., uniqueness or a business rule)
- this happens because constraints are based on data that was not yet present at the time of each write


Solution: Materialize Conflicts

- populate rows in a table with items where could be a conflict —> we can use locks
- race condition will still happen since two transactions want same row/object but lock fixes phantom write
- how to know what null row should be created —> think in advance (ex. create all available meeting rooms before making them open to be booked)

These are both concurrency problems, but they are different from dirty writes.

1. Write Skew

Write skew happens when two transactions read the same related data, make decisions based on what they read, and then update different rows.

Example: imagine two doctors must be on-call.

Doctor A → on_call = true
Doctor B → on_call = true

Rule:

At least one doctor must remain on call.

Two transactions run at the same time:

Transaction 1             Transaction 2

Read A + B
Both are on call          Read A + B
                          Both are on call

Set Doctor A = OFF        Set Doctor B = OFF

COMMIT                    COMMIT


Final state:

Doctor A → OFF
Doctor B → OFF

The database allowed both transactions because they modified different rows, but together they violated the business rule.

That's write skew.

2. Phantom Write ( When two transaction write or commit the new row that conflicts)


Phantom write: two concurrent transactions perform writes based on a predicate/range of rows, and their writes can conflict because the set of matching rows changes.

Phantom read = the same query sees a different set of rows because another transaction committed an insert/delete/update affecting the query's range.	

The term phantom write is less standardized than "write skew." In database discussions, it commonly refers to a transaction's operation being affected by rows that appear/disappear or change between executions of a range/predicate-based query.

For example:


SELECT *
FROM employee
WHERE salary > 50000;

Transaction A sees:

Rahul → 60000
Kumar → 70000

Meanwhile, Transaction B inserts:

Priya → 80000

and commits.

If Transaction A runs the same query again, it may now see:

Rahul → 60000
Kumar → 70000
Priya → 80000  ← new row

The newly appearing row is normally called a phantom row, and the phenomenon is a phantom read. Some sources use "phantom write" more broadly for write anomalies involving predicate/range-based sets.

🧠 For your learning
Problem	Simple idea
Dirty write	Two transactions overwrite the same uncommitted data
Write skew	Two transactions update different rows but together break a rule
Phantom read	A repeated query sees new/disappearing rows

The write skew example is the one you should especially remember for database isolation.



Materialized conflict --- Solution 

Materialized conflict means a conflict that becomes visible as an actual row/value conflict in the database, rather than being only an abstract conflict between two transactions' assumptions.

In simple terms:

Transaction A ──┐
                ├──> Both affect the same actual data
Transaction B ──┘
                    ↓
             Materialized conflict
			 
			 Example

Suppose:

Employee 101 → salary = 50,000

Two transactions try to change the same row:

Transaction A:
UPDATE employee SET salary = 60,000 WHERE id = 101;

Transaction B:
UPDATE employee SET salary = 70,000 WHERE id = 101;

They have a direct/materialized conflict because both are trying to modify the same concrete row.

Compare that with write skew:

Transaction A → updates Doctor A
Transaction B → updates Doctor B

They don't directly modify the same row, but their combined changes can violate a rule.

🧠 Simple distinction

Materialized conflict → conflict on actual data/rows.
Write skew → conflict through a business rule involving related data.
==========================================================================================================================================


# system-design-jorden-has-no-life
