# How do you decide which database?

The performance of your application can suffer if you choose the incorrect database type, and going back on a bad choice can be time-consuming and expensive.

Before we dive into the factors to take into account when choosing an appropriate database, let’s examine the characteristics of the most widely used databases.

In a 𝗿𝗲𝗹𝗮𝘁𝗶𝗼𝗻𝗮𝗹 𝗱𝗮𝘁𝗮𝗯𝗮𝘀𝗲, data is organized in rows and columns where a row represents a record and its data fields are stored in columns. They are ideal for when ACID compliance is required, and a predefined schema can be created.

With 𝗰𝗼𝗹𝘂𝗺𝗻𝗮𝗿 𝗱𝗮𝘁𝗮𝗯𝗮𝘀𝗲𝘀, records are stored as columns rather than rows. This makes them very performant for analytical purposes where complex queries are run across large datasets; especially those that contain aggregate functions.

In a 𝗱𝗼𝗰𝘂𝗺𝗲𝗻𝘁 𝗱𝗮𝘁𝗮𝗯𝗮𝘀𝗲, data is stored in a semi-structured format such as JSON. They offer a flexible and schema-less approach which makes them a great choice for data with complex or continually changing structures.

𝗚𝗿𝗮𝗽𝗵 𝗱𝗮𝘁𝗮𝗯𝗮𝘀𝗲𝘀 are optimized for storing and querying highly connected data. Records are represented as nodes and relationships as edges. Under the hood, they use graph theory to traverse relationships between nodes to power performant queries.

𝗞𝗲𝘆-𝘃𝗮𝗹𝘂𝗲 𝘀𝘁𝗼𝗿𝗲𝘀 are a simple form of storage where values are inserted, updated, and retrieved using a unique key. They are more commonly used for small datasets and often temporary purposes such as caching or session management.

𝗧𝗶𝗺𝗲-𝘀𝗲𝗿𝗶𝗲𝘀 𝗱𝗮𝘁𝗮𝗯𝗮𝘀𝗲𝘀 are ideal for time-stamped data that are queried and analyzed in relation to time. They provide built-in time-based functions that assist in analyzing large datasets over time.

Each database type has been optimized for specific use cases.

It's important to thoroughly consider the correct database for your use case as it can impact your application’s performance.

𝗕𝗲𝗹𝗼𝘄 𝗮𝗿𝗲 𝗰𝗼𝗻𝘀𝗶𝗱𝗲𝗿𝗮𝘁𝗶𝗼𝗻𝘀 𝘁𝗵𝗮𝘁 𝘀𝗵𝗼𝘂𝗹𝗱 𝗯𝗲 𝗺𝗮𝗱𝗲:

🔸 How structured is your data?

🔸 How often will the schema change?

🔸 What type of queries do you need to run?

🔸 How large is your dataset and do you expect it to grow?

🔸 How large is each record?

🔸 What is the nature of the operations you need to run? Is it read-heavy or write-heavy?

Use these questions as a starting point for your analysis. Take the time to investigate your use case and ask questions to your stakeholders and end-users when necessary.

It's important to invest as much time in this decision as needed. Choosing the wrong database type can be detrimental to your application’s performance, and difficult to reverse.

![image](https://github.com/r-shreesha/Interview-Prep/blob/main/Design_Diagrams/Database_Types.jpeg)