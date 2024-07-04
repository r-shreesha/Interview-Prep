# Framework

[!image](https://github.com/r-shreesha/Interview-Prep/blob/main/Design_Diagrams/How_to_answer_Cheat_Sheet.jpeg)

## 1. Define the problem space - 5 min
  - Ask lots of questions to shrink the scope of the problem from broad and vague to narrow and specific with system constraints
  - Don't jump into design
  - Clarify both Functional and non-functional requirements
  - State your assumptions
  - Are you creating the system from scratch or using some pieces from an existing system ?
  - Who are our consumers/clients?
  - Non functional requirements may be tied to Business objectives or User experience
      - Scalability
      - Availability
      - Consistency
      - Speed
      - Security
      - Reliability
      - Cost
- If we have identified lot of design constraints, then pick the one that is critical enough and explain the reasoning
- If the interviewer wants to discuss a specific constraint, ask them

#### Estimate the amount of data we are dealing with 
- Storage size
- Bandwidth requirements
- User volume
- Typical user behavior

## 2. High level Design - 10 min
Layout fundamental components of the system to achieve the desired functionality keeping in mind the constraints identified in Step #1

#### API Design
Each system requirement should translate to one or more APIs.
Choose the type of the APIs - REST/SOAP/GraphQL/RPC and explain why.
Establish Request parameters and Response type 
Think about how the Client and WebServer will communicate

Create a High Level Design Diagram and explain how the control and data flow looks like in the system
Do not dive into the scalability yet.

## 3. Deep Dive into the design - 10 min

Interviewer might ask you to focus on specific part of the system. But DO NOT EXPECT THEM TO DRIVE THE CONVERSATION HERE.
Start by thinking how non-functional requirements impact your design choices
Example: 
Online system requires fresh data - Speed up Data Ingestion and Queries
Amount of data to store is large - Partition database to balance storage and query traffic
Load Balancer to distribute Read/Write traffic

You present different design options with pros and cons and why you prefer one over another.

## 4. Identify Bottlenecks and Scaling Opportunities - 10 min
Zoom out and consider if the system can operate under various conditions and has room to support additional growth
Single point of failure - Robustness and enhance system's availability?
Data valuable enough to Replicate? If so, do we need to keep all the versions?
Global service? Multi Geo DCs
Edge cases? Peak time usage? Hot users? 
How to scale the system to support 10 times more users?

Horizontal sharding, CDN, Caching, Rate Limiting, SQL/NoSQL DBs etc

## 5. Review and Wrap up - 5 min

Summarize
List major decisions and justifications
Re iterate on the trade offs made in Space or time complexity
Check if the design satisfies ALL the requirements periodically throughout the interview
You can also identify directions for further improvement


