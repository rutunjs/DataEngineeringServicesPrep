## AWS RedShift
<details>
  <summary>What is Redshift ?</summary>
  
  - It is Fully-managed, petabyte scale data warehouse service.
  - <b>Key Features </b>
    - Design of OLAP (Online Analytical processing) not for OLTP (Online transactional processing)
    - Columnar format and compression
    - JDBC, ODBC, SQL support
    - 10x better performance then other warehouse services
    - Massivally parallel processing (MPP)
    - Monitor using Cloud watch and cloud trail
    - Built in replications
    - Support complex queries, generate reports and get insights into their data
    - Cross region data sharing 
      - Share live data across Redshift clusters without copying
      - Requires new RA3 node type
      - Secure, across regions and across accounts
    - <b>Scaling </b> 
      - On demand Vertical and horizonatal Scaling 
      - During scaling old cluster will remain available for read opration
      - <b>Few minutes down times </b> to flip c- name records
      - Data move parallel to new compute nodes
    - <b>Data Durability</b> 
      - Replication within cluster
      - Backup to S3 bucket and Async replication to other region 
      - Automated and manual snapshots
        - If cluster is KMS encrypted and we have snapshot of that. For cross region copy we need to create KMS key in another region and then need to specify Unique name of snapshot copy grant and KMS key Id for which we are creating copy grant.
        - In source region need to enable copy of snapshot to copy grant  
      - Failed Nodes automatically replace 
      - RA3 family support multi AZ. Other support single AZ like DC2 
    - Parameter Group
      - It is collection of setting that is apply to all database within redshift cluster. It control various aspect of database behaviour. such as query timeout, date style and logging option.
    - Data API
      - Secure HTTP endpoint for provisioned and serverless both 
      - Asynchronus 
      - Not require to manage connection (No driver needed)
      - Password will not sent by API - it stored in secret manager
      - Can be used by SDK 
      - Cloud trail can capture API call logs
      - Max Limit 
        - Query duration : 24 hours
        - Active query : 500 
        - Query result size (G zip) : 100 MB
        - Result retention time : 24 hours
        - Query statement size : 100 KB
        - Packet of query : 64 KB 
        - Client token retention time : 8 hours
        - Transaction/second quotas per API (30 for execution statement)
      - Type of API calls
        - ExecuteStatement, BatchExecuteStatement 
        - DescribeStatement, DescribeTable 
        - GetStatementResult, CancelStatement 
      - Cluster must be in VPC

</details>

<details>
  <summary>RedShift Architecture</summary>

  - Leader node, Compute node and Node slice
  - Nodes type : Dense storage and Dense compute
  - [AWS diagram for Architecture](https://docs.aws.amazon.com/redshift/latest/dg/c_high_level_system_architecture.html)
  - Leader node 
    - The leader node manages communications with client programs and all communication with compute nodes. It parses and develops execution plans to carry out database operations, in particular, the series of steps necessary to obtain results for complex queries. Based on the execution plan, the leader node compiles code, distributes the compiled code to the compute nodes, and assigns a portion of the data to each compute node.
    - <b>Note : </b> The leader node distributes SQL statements to the compute nodes only when a query references tables that are stored on the compute nodes. All other queries run exclusively on the leader node. Amazon Redshift is designed to implement certain SQL functions only on the leader node. A query that uses any of these functions will return an error if it references tables that reside on the compute nodes.
  - Compute nodes
    - The compute nodes run the compiled code and send intermediate results back to the leader node for final aggregation.
    - Each compute node has its own dedicated CPU and memory, which are determined by the node type. As your workload grows, you can increase the compute capacity of a cluster by increasing the number of nodes, upgrading the node type, or both
  - Node slices
    - A compute node is partitioned into slices. Each slice is allocated a portion of the node's memory and disk space, where it processes a portion of the workload assigned to the node. The leader node manages distributing data to the slices and apportions the workload for any queries or other database operations to the slices. The slices then work in parallel to complete the operation.
    - [More detail or Slice](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes)  
</details>


<details>
  <summary>Distriution Style in RedShift</summary>
  
  1. Auto
    - Redshift will figure out based on size of data. It will automatically change style when table grows with data
  2. Even
    - It will distribute row in <b>round robin format across slices </b>
  3. Key 
    - Its depend on one column for data distibution
  4. All 
    - Entire table will copied to every node
</details>

<details>
  <summary>Sort key  in RedShift</summary>
  
  - THIS IS IMPORTANT FEATURE WHILE CREATING TABLES AS WITHOUT THAT QUERY WILL NOT OPTIMIZE, IT INVOLVE MORE I/O OPERATIONS. ABESNCE OD SORTKEY MEANS DATA IS NOT SORTED AND IT SLOW QUERY PERFOMANCE
  1. Interleaved sort key
    - It is design to improve query performance for table where multiple columns are frequently use with varying filter
    - Data is distributed evenly accross multiple dimensios, making it more flaxible
    - Useful for tables where you have range o queries filtering on diffrent columns
    ```
    CREATE TABLE MYTABLE (
      id INT,
      value VARCHAR(255),
      date TIMESTAMP
    )
    INTERLEAVED SORTKEY (id,date)
    ```
  2. CompOund Sort key
    - It priotrize the first column in the sort key list which is ideal for list queries that filter or join using that leading column
    - leading to optimize sequencial access
    - best for queries that filter on join the leading columns  
    ```
    CREATE TABLE MYTABLE (
      id INT,
      value VARCHAR(255),
      date TIMESTAMP
    )
    COMPOUND SORTKEY (id,date)
    ```
</details>

<details>
  <summary>Import or export data in redshift</summary>
  
  1. Recommend to Use Copy command to import data from S3. No metter if it is in PetaByte or ExaByte scale
      - Copy target-table from 'S3://bucketpath' IAM_Role 'role ARN' FORMAT AS CSV PARTITION BY (COLUMNNAME)
      - Ensure access of s3 bucket in IM Role 
      - We can use RDS, DynamoDB Tables to copy all data using this command 
        - COPY MY_TABLE FROM 'DYNAMODB://TABLE' IAM_ROLE 'Arn'
        - <b>RDS</b>
          - COPY MY_TABLE FROM 'arn:aws:rds:region:account-id:db:mydb' IAM_ROLE 'Arn' USERNAME 'root' PASSWORD 'pass' QUERY 'select * from source_table'; 
  2. To export data or Query from readshift we can use UNLOAD command 
    - UNLOAD (QUERY) To 's3://path' IAM_ROLE 'Role ARN' FORMAT AS PARQUET PARTITION BY (COLUMNNAME)
    - We can use Partition by (Year, Month, Day) based on requirement
  3. enhance VPC routing for security
  4. Auto copy from S3 bucket if incremental load 
  5. Use Zero ETL integration in RDS and Redshift - replicate data in redshift cluster : [Link](https://repost.aws/articles/AR5c_j088bT76j2v5hAWaljw/set-up-cross-account-zero-etl-integration-in-the-same-region)
  6. Redshift streming ingestion - kinesis Data stream or MSK
    - Kinesis Data stream -> Firehose -> S3 -> external schema -> Query or create view 
    - We can push data directly from stream to redshift table
      - CREATE EXTERNAL SCHEMA kinesis_schema FROM KINESIS IAM_ROLE 'redshift role arn' STREAM_NAME 'streamname'
  7. If Data is in another table then use Insert into ... select * or Create table as
    - CREATE TABLE target_table AS select * from source_table with NO DATA;
      - WITH NO DATA copy only schema not data. remove it if you want to copy data as well 
    - INSERT INTO target_table select * from source_table 
  8. Compression will automatically apply. we dont need to mention it in query
  9. <b>Narrow table </b>
    - When large number of rows but relatively few columns are there in table. 
    - it is challenging to optimize 
      - Hidden mata data
      - Recommend to copy data in single command
      - if Table loaded in multiple command then use Vacuume operation. 
      - Do regular maintanance
      - Use ANalyze command to update statistic and optimized query
</details>


<details>
  <summary>Redshift Workload Management (WLM)</summary>
  
  - This feature that allow us to manage and priotize concurrent queries and user workload to optimize performance and resource utilization.
  - Manual WLM Configuration
    - <b> Key Features </b>
      1. Query queue
        - Define multiple query queue to manage diffrent type of workloads
      2. Resource allocation
        - allocate CPU and memory resource to different queues based on needs
      3. Priority management
        - Set priorities for queries to ensure critical queries get resource they need
        - prioritize long query vs short queries
      4. Concurrency limit
        - Control number of concurrent queries to prevent resource contention
      5. User Group
        - Assign user to specific group and manage their work load seperately 
      6. Define Super user queue with concurrency level 1
    
    - <b>Benifits </b>
      1. Improved performance
      2. Resource optimization
      3. Flexibility
    
    - Configuration Example
      ```
      CREATE WLM CONFIGURATION my_wlm_config
      WITH (
        MAX_COUNT_PER_QUEUE =5,
        QUEUE = (
          ('high_priority',1,50)
          ('default', 2, 50)
        )
      );

      -- Assign WLM configuration to the cluster
      ALTER CLUSTER my_cluster SET WLM_CONFIG = 'my_wlm_config'
      ```
    - This will create 2 queue. One will create high priorities queries and another for default queires. each with max concurrency limit 5
  - How query Assign to Perticular Queue?
    1. Queries are assigned to queues based on the rules you set. For example, you can create rules based on:
      - Query group (e.g., tagging queries with specific groups).
      - User group (e.g., assigning queries from certain user groups to a particular queue).
    2. Query Routing:
      - Queries submitted by users are checked against WLM configuration.
      - Based on user-defined rules or automatic classification, they are routed to the appropriate queue.
      - If a queue has free slots, the query starts execution. Otherwise, it waits in the queue.
- Automatice WML
    - Instead of manually configuring queues, you can enable automatic WLM. In this case, Redshift optimizes resource allocation dynamically, providing better resource management for varying query types.
    - Redshift’s Automatic WLM dynamically manages memory and concurrency, automatically adjusting resource allocation based on query performance and workload.
    - With automatic WLM, queries don’t have fixed memory or slots assigned to them, allowing more flexibility for varying workloads.
- Short query Accelaration
  - It is automated feature with WLM Prioritize short-running queries over longer-running ones
  - Short queries run in a dedicated space, won’t wait in queue behind long queries
  - Can be used in place of WLM queues for short queries
  - Uses machine learning to predict a query’s execution time
  - Can configure how many seconds is “short”
  - It allocate more resource to this queries, running them parellel to speed up execution
  ```
  ALTER WLM CONFIGURATION FOR ACCOUNT SET SQA_PRIORITY = HIGH;
  ```
- <b> Limitations </b>
  - For Manual Max 8 Queues per cluster for auto it will assign dynamically
  - Each Queue has a Maximum of 50 concurrency slots. If the number of queries exceeds the available slots, queries will be queued, and they will not start until slots are available.
  - In manual WLM, you must assign memory allocations for each queue. Poor memory allocation (e.g., allocating too much to one queue and too little to others) can lead to performance issues where some queries wait too long while others hog resources.
  - You can set query timeouts for each queue. If a query exceeds this timeout, it will be canceled. While this helps prevent long-running queries from consuming too many resources, it can be a limitation for complex queries that require more time.
  - You need to ensure that timeout settings are appropriate for the types of queries you expect
  - Too many concurrent queries running on a cluster can create significant overhead in managing the queue, leading to performance degradation. The overhead increases as queries become more complex and require higher resources (memory and CPU).
  - With manual WLM, the configuration is static, meaning once you set concurrency and memory for a queue, they are fixed until you manually change them. This rigidity can be limiting if your workload fluctuates.
  - uery assignment to WLM queues is limited to the use of user groups and query groups for routing. If you need more complex logic to route queries to specific queues based on multiple factors, the default WLM may not be sufficient.
  - Resource allocation (e.g., CPU and memory) between queues can cause resource contention. For example, if one queue consumes too many resources, it may starve other queues, resulting in slower performance for lower-priority workloads.
  - Once a query is assigned to a queue, it remains there until it completes, even if another queue becomes available. Queries cannot be dynamically moved between queues based on available resources.
  - Long-running queries can block other queries in the same queue if there are insufficient concurrency slots. This can lead to queue backups where shorter queries must wait for the longer ones to complete.
</details>

<details>
  <summary>Concurrency Scaling in Redshift</summary>

  - Automatically add and remove capacity to handle suddn increase in concurrent queries
  - When cluster run out of capacity to handle incoming queries then redshift add more compute resource in form of additional concurrency sacaling cluster
  - ideal for unpredictable spike
  - Support virtually unlimited concurrent user
  - WLM Queue manage which queries are send to concurrency scaling cluster  

</details>

<details>
  <summary>Resize Redshift Cluster</summary>

  1. Elastic resize
    - Quickly add or remove nodes of same type instance <b>((It *can* change node types, but not without dropping connections. it creates a whole new cluster)</b>
    - Cluster is <b> down for a few minutes </b>
    - Tries to keep connections open across the downtime
    - Limited to doubling or halving for some dc2 and ra3 node types
  2. Classic resize
    - Change node type and/or number of nodes
    - Cluster is read-only for hours to days
</details>

<details>
  <summary>Redshift Security</summary>

  - follow list previllagies access permission
  - Keep cluster software updated 
  - Monitor cloudwatch metrix regularly

  1. Data at rest (AWS-256, KMS) and in-transist by TLS/SSL
  2. Access control
    - IAM User, Group and Roles for specific permission
    - VPC security to manage network level access
  3. Monitoring and loging 
    - Cloud trail 
    - Cloud watch
  4. Back up and Restore - Takes auto matic and manual backups
  5. Network Security 
    - VPC endpoint 
    - Private link
  6. Hardware Security Module (HSM)
    - Need to manage harware provided key 
    - Need to manage while creating cluster
    - Helps to meet regulatory requirement for data encryption and key management
    - It provide addtional layer of security by storing key in hardware device
    - Must use a client and server certificate to configure a trusted connection between Redshift and the HSM
    - If migrating an unencrypted cluster to an HSM-encrypted cluster, you must create the new encrypted cluster and then move data to it.
  7. Use the GRANT or REVOKE commands in SQL to define access
    ```
    GRANT SELECT ON TABLE TABLENAME TO USER_AC
    REVOKE SELECT ON TABLE TABLENAME FROM  USER_AC
    -- GROUP 
    GRANT SELECT ON TABLE TABLENAME TO GROUP USER_GROUP
    REVOKE SELECT ON TABLE TABLENAME FROM GROUP USER_GROUP
    ```
</details>



<details>
  <summary>Redshift materialize views</summary>

  - Materialized views are especiallly useful for scenarios where you need to run predicatable and repeated queires on large datasets. They help in speeding up analytics workloads and imporving overall efficiency.
  - <b> Features </b>
    - Precomputed Result
    - Incremental updates
      - Only changes since the last referesh are processed
    - Support for complex queries 
      - Involving joins, aggregations and other source incentive operations
    - Automated refresh
      - You can setup automatic referesh schedules to keep the materialized view up to date
      - It is not time bound. Insted it trigger based on changes in underlaying data.
    - Backup and restore 
      - It can be included in automated and manual cluster snapshot
    - Provide a way to speed up complex queries in a data warehouse environment, especially on large tables
    - You can query materialized views just like any other tables or views.
    - They're particularly beneficial for predictable and recurring queries, e.g., populating dashboards like Amazon QuickSight
    - Example
      ```
      CREATE MATERIALIZED VIEW tbl_view AS
        AUTO REFRESH YES
        select catgroup,
        sum(qtysold) as sold
        from category c, event e, sales s
        where c.catid = e.catid
        and e.eventid = s.eventid
        group by catgroup;
      ```

  - <b> Benefits </b>
    - Improved query performance
    - Reduced load on source tables
    - Consistency
    - Cost Efficiency
    - Simplified reporting  
</details>

<details>
  <summary>Redshift serverless</summary>

  - It is feature that simplifies running and scaling analytics without provision and managing data warehouse.
  - <b> Key Features</b>
    1. Automatic Scaling
      - Redshift serverless automatically provision and scales data warehouse capacity to handle unpredicatable workloads. 
      - Capacity Management
        - Redshift serverless automatically adjusts capcity based on workload demand. This means you dont have to manually scale your resource up or down.
      - Concurrency scaling
        - It can handle spikes in concurrent queries by scaling out to addtional resource as needed.
    2. Pay-As-you-go model
      - Pay only for capacity you use
      - Pay for RPU hours (per seconds) + Storage
      - Base RPU's default to AUTO. Can adjust from 32-512 RPU to improve query performonance
      - Max RPU - Can set usage limit to control cost or increase it to improve throughput.
    3. Ease of use
      - Ideal for data analyst, developers, data scientist who want to get insghts from data quickly
    4. High performance
      - Deliver fast performance for demanding analytics workloads.
    5. Uses ML to maintain performance across variable & sporadic workloads
    6. Easy spinup of development and test environments
    7. Easy ad-hoc business analysis - run queries without worrying about infrastruture.
    8. You get back a serverless endpoint, JDBC/ODBC connection, or just query via the console’s query editor.
    9. Can manage and snapshots & recovery point after creation.
  - </b> Requiremnet </b>
    - Need an IAM role with this policy
      - Database name
      - Admin user credential 
      - VPC
      - Encyption setting - using KMS
      - Audit logging  
  - Not have in redshift serverless 
    - Parameter group
    - WORK load management
    - AWS parter integration
    - Maintanance window track
    - No public endpoint need to use inside VPC
  - Monitoring
    - Use system views for history 
    - Cloudwatch logs - connection and user logs 
    - Cloudwatch metrics - 
      - QueriesCompletedPerSecond, QueryDuration, QueriesRunning etc.
      - Dimension : Database name, latency, Query type stage
</details>

<details>
  <summary>Redshift Data sharing Feature</summary>

  - Securely share live data across Redshift clusters for read purposes without needing to create copies of that
  - It is secure and provide read only access. Data owner maintain control over that data.
  - Allow to querying cross cluster data, which is use ful for corss functional analysis.
  - <b>Licensing data access in AWS Data Exchange</b>
  - Both must be encrypted 
  - Must use RA3 Cluster nodes
  - Cross region data sharing incur data charges
  - <b> Key concepts </b> 
    - Data share
      - A datashare is associated with specific database in redshift cluster
      - type of datashare
        - Standard
        - AWS data exchange
        - AWS lake formation managed
    - Producer cluster
      - It provide read only access to the data
      - Can share schema, table, view and UDFs
      - Fine grain access control
      - Isolation to ensure producer performance unaffected by consumers
    - Consumer cluster
      - it received shared data, It can query data without duplicating it
</details>


<details>
  <summary>Readshift Lambda UDF</summary>

  - Use custom function in aws lambda function in side sql queries.
  - Must allow user to run external function written in specific lanaguge
  ```
  -- grant function access
  GRANT USEAGE ON EXFUNC my_lambda_udf TO user_3;
  -- In redshift
  CREATE EXTERNAL FUNCTION my_lambda_udf (arg1 INT, arg2 VARCHAR)
  RETURNS INT
  LAMBDA 'ARN'
  IAM_ROLE 'ARN'

  -- USE IN QUERY 
  SELECT my_lambda_udf(col1,col2) from mytable
  ```
</details>

<details>
  <summary>Redshift Fedrated queries</summary>

  - Query and analyze across databases, warehouses, and lakes
  - Query database from external database like RDS, Aurora 
  - Offloads computation to remote databases to reduce data movement
  - How it works
    - Connectivity setup 
      - Need to put them in same VPC subnet or VPC peering
      - Credential must be in secret manager
      ```
      CREATE EXTERNAL SCHEMA ext_schema
      FORM POSTGRES
      DATABASE 'DBNAME'
      URI 'ENDPOINT'
      IAM_ROLE 'ARN OF REDSHIFT FEDRATEDROLE'
      SECRET_ARN 'secrat arn of creadential'


      -- run fedrated query 
      SELECT * FROM ext_schema.remote_table;
      ```
    - Meta data retrival
    - Query execution
      - Issue query to remote database and retrives result rows data.
    - Result processing
      - result row will distribute to redshift node for further processing
    - The SVV_EXTERNAL_SCHEMAS view contains available external schemas
    - It contain only read only access
    - cost will be on external DB

</details>

<details>
  <summary>Red shift System table and Views</summary>

  - It contain info about how redshift functioning 
  - STL_ALERT - Contain information about alert generated by redshift
  - STL_APPLICATION_LOG - Log application level events and error
  - STL_CONNECTION - detail about active connection
  - STL_DDL_LOG - logs of CREATE, ALTER and DROP
  - STL_EXPLAIN - contain execution plan of query
  - STL_QUERY - provide execution information about db query
  - STL_QUERY_METRICS - contain cpu Usage and rows processed
  - STL QUERYTEXT - Capture query text for SQL command
  - STL_TR_CONFLICT - logs Lock conflict
  - <b>STL_BCAST</b> - log information about netork activity during query execution
  - STL_WLM_RULE - contain info about workload management
  - SVL_STATEMENT - provide details about individual sql statement executed
  - SVL_QUERY - detail information about query excution including statistic
  - SVL_SNS - logs message sent to SNS service
  - SVL_TOP_EVENTS - List top event in term of time spent
  - SVL_TOP_WAIT_EVENTS - list the top wait events and their duration
  - SVL_QUERY_HISTORY - provide history of executed queries
  - SVL_QUERY_REPORT - Generate detail report on query performance

  '''
  SELECT * FROM STL_QUERY WHERE query = ''
  ''' 

  - SYS view - Monitor query and workload usage
  - STV tables: Transient data containing snapshots of current system data
  - SVV views: metadata about DB objects that reference STV tables
  - STL views: Generated from logs persisted to disk
  - SVCS views: Details about queries on main & concurrency scaling clusters
  - SVL views: Details about queries on main clusters
</details>

<details>
  <summary>DB Link Feature</summary>

  - This feature allow you to query in  <b>Postgres</b> compitible remote database with in redshift queries.
  - Access live data from that database without loading it to Redshift
  - Easy integration 
  - Steps to use it 
    1. install DBlink extension in redshift cluster 
      - CREATE EXTENSION dblink;
    2. Create database link
      - SELECT dblink_connect('rds_link','host=myhost port=5432 dbname=mydb user=myuser password=mypass');
    3. Query remote database 
      - select * from dblink('rds_link','select * from tablename') AS t(id INT, name VARCHAR(255))
</details>

<details>
  <summary>General Use cases of Redshift </summary>
  
  1. Business intelligence 
    - Complex queires, report generate, insights into data
  2. Data warehousing 
    - Store and analyze data from varoius sources
  3. Marketing analytics
    - Customer behaviour, Campaign performance, Marketing attribute to optimize staretagy 
  4. Financial reporting
    - Store and analyze historical data for reporting and compilence
  5. Health care analytics
    - Clinical quality, Finanacial performance, operational efficiency
  6. Sales and Revenue analysis
    - Analyze global data and identify global trend of products 
  7. Supply chain management
    - Track and analyze chain data for optimze logistic and inventory
  8. Social media analysis
    - Social trends, Ad impression, Click and audience engagement
  9. Data lake integration
    - Integrate and analyze data accross data lake 
  10. Gaming analytics
    - Player behaviour 
</details>

<details>
  <summary>What is Redshift Spectrum</summary>

  - It is a feature of Amazon readshift that allow us to run SQL queries on S3 without loading it to Redshift tables. We can analayze large amount of structure or semi structure data in s3 using SQL queries.
  - <b>Key Features</b>
    - Query Data in S3
    - Massive Parallel Processing
    - Sepration of Compute and storage
     - Scale compute and storage independatly
    - No Data Duplication 
        - without data duplication to store it to other place
    -  Compatibility
        - Support various format like ORC, Parquet, Json, CSV, TSV, Sequence, Groovy
        - Support Gzip and Snappy compression
    - Limit less concurrency
    - Horizonal scaling
  - <b>Use cases: </b>
    - Data lake analytics
    - Historical data analytics
    - Cost efficency
</details>


<details>
  <summary>Best Practices for Redshift</summary>

  1. Avoid using select * from - Use only necessary columns
  2. Use CASE statement for aggrgation 
  3. Minimize or avoid corss join - very resource incentive
  4. Use sub-queries wisely - use it when row is less then 200 
  5. Optimize join - use it in efficent way
  6. Analyze and Vacuum Regularly 
    - Analyze - for update statistic - Run it after bulk insert, update deletes
      - ANALYZE TABLENAME; - for entire table
      - ANALYZE TABLE (COL1,COL2); - for specific column
      - ANALYZE SCHEMA my_schema; - for schema only
    - Vacuum - Reclaim space and recognize storage - periodically schedule to maintain performance
      - VACUUM FULL TABLE - Reclaim disk space and re-sort data
      - VACUUM SORT ONLY TABLE - only resort data without reclaim space
      - VACUUME DELETE ONLY TABLE - Reclaim space for only deleted rows 
      - VACUUME REINDEX TABLE - Rebuild interleaved sort key
  7. Use UNLOAD insted of SELECT for large data export
  8. Parquet is 2x faster to unload and consumes up to 6X less storage
</details>
