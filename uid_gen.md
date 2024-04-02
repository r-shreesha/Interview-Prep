# Unique ID Generator

## Clarifying questions

* What are the characteristics of unique ids?
  - IDs must be unique and sortable
* For each new record, does ID increment by 1?
  - The ID increments by time but not necessarily only by 1. IDs created in the evening are larger than those created in the morning on the same day.
* DO IDs only contain numerical values?
  - Yes
* What is the ID length requirement?
  - ID should fit into 64-bit
* What is the scale of the system?
  - The system should be able to generate 10,000 IDs per second.

## Possible Approaches

1. Multi-master replication
2. Universally unique identifier
3. Ticket Server
4. Twitter snowflake approach

***
### Multi-master replication
***
Usees databases' `auto_increment` feature. We can increase the next ID by `k` instead of `1`, `k` being the number of database servers in use.

#### Cons
* Hard to scale with multiple data centers
* IDs do not go up with time across multiple servers
* It does not scale well when a server is added or removed

***
### UUID
***
UUID is a 128 bit number used to identify information in computer systems. They have a very low probability of getting collision. Example: `09c93e62-50b4-468f-bf8a-c07e1040bfb2`

In this design, each web server contains an ID generator and a web server is responsible for generating IDs independently without coordination between servers.

#### Pros
* Generating UUID is simple. No coordination is required between servers
* The system is easy to scale because each web server is responsible for generating IDs they consume. ID gen can scale easily with web servers.

#### Cons
* ID are 128 bit long (our requirement is 64 bits)
* IDs do not go up with time
* IDs could be non-numeric

***
### Ticket Server
***
Flicker developed ticket servers to generate distributed primary keys. 

The idea is to use a centralized `auto_increment` feature in a single database server(Ticket Server). 

#### Pros
* Numeric IDs
* Easy to implement and it works for small to medium scale applications

#### Cons
* Single Point of failure, if ticket server goes down. Setting up multiple ticket servers introduce new challenges like data synchronization.

***
### Twitter snowflake approach
***
Instead of generating an ID directly, we divide an ID into different sections.

* Sign bit: 1 bit. It will always be 0. Reserved for futue uses
* TimeStamp: 41 bits. Milliseconds since the epoch or custom epoch.
* Datacenter ID: 5 bits, which gives us 32 data centers
* Machine ID: 5 bits, which gives us 32 machines per datacenter
* Sequence number: 12 bits. For every ID generated on that machine/process, the sequence number is incremented by 1. This number is reset to 0 every millisecond

Any changes in datacenter IDs and machine IDs require careful review since an accidental change in those values can lead to ID conflicts.

## Wrap up

If there is extra time at the end of the interview, additional talking points include:
* Clock synchronization: We assume that ID generation servers have the same clock. It might not be true when a server is running on multiple cores/Multi-machine scenarios. Popular solution is `Network Time Protocol`.
* Section legnth tuning: fewer sequence number but more timestamp bits are effective for low concurrency and long-term applications
* High availability: Since an ID generator is a mission critical system, it must be highly available.
