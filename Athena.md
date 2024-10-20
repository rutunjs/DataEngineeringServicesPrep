## AWS Athena
<details>
  <summary><b>What is Athena?</b></summary>

  - Amazon Athena is an interactive query service provided by AWS that enables you to analyze data stored in Amazon S3 using standard SQL without the need for setting up or managing any infrastructure. Athena is serverless, meaning there’s no need to manage servers, clusters, or databases—AWS handles the underlying infrastructure automatically.
  - Athena does not directly support Apache Spark for querying. Athena is built on Presto, an open-source distributed SQL query engine, and is designed for ad-hoc querying of data in Amazon S3 using SQL. Presto is optimized for interactive queries, while Spark is more suitable for large-scale data processing and complex ETL workflows.
  - <b>Key Features</b>
    - Serverless: Athena doesn’t require any server setup or management. You can start querying data immediately after setting up permissions.
    - SQL-Based: Athena uses SQL to interact with data, making it easy for users familiar with SQL to perform queries.
    - Data Sources: Primarily designed to query data stored in Amazon S3, but it can also access other AWS data sources like Amazon RDS, Amazon Redshift, AWS Glue Data Catalog, and external data sources via connectors (such as JDBC).
    - Supports Multiple Data Formats: Athena can query various data formats, including CSV, JSON, Parquet, ORC, and Avro. This flexibility allows for optimized querying by using more efficient formats like Parquet and ORC for large-scale data.
    - Built-in Integration with AWS Glue: Athena integrates with the AWS Glue Data Catalog, which serves as a metadata store. You can use Glue to automatically crawl and catalog your data, making it available for Athena queries.
    - Pay-per-Query Pricing: Athena charges you based on the amount of data scanned per query (currently $5 per TB of data scanned). You can reduce costs by storing data in columnar formats (like Parquet or ORC) and compressing it.
    - Fast Query Execution: Athena uses a distributed query engine (based on Presto), which allows it to execute queries quickly over large datasets.
    - Secure Data Access: Athena integrates with AWS IAM for access control, supports encryption for both data at rest and in transit, and can be integrated with AWS Lake Formation for fine-grained data access control.
    - Supports Joins and Complex Queries: Athena supports SQL queries, including joins, window functions, grouping, and other complex SQL constructs, making it suitable for advanced data analysis.
    - Use Cases: Athena is often used for ad-hoc querying of log files, performing data lake analytics, data exploration, and reporting without the need for complex ETL processes or data movement.
  - <b> How Amazon Athena Works: </b>
    - Store Data in S3: Your data (structured, semi-structured, or unstructured) is stored in Amazon S3 in formats like CSV, JSON, Parquet, ORC, etc.
    - Define a Schema: Athena requires a schema to understand the structure of your data. You can define schemas manually, or use AWS Glue Crawlers to automatically discover and catalog your data.
    - Query Data Using SQL: Once your data is stored and the schema is defined, you can query the data using SQL commands directly in the Athena console, via the Athena API, or using JDBC/ODBC connectors for external tools like BI tools (e.g., Tableau, QuickSight).
    - Result Delivery: Query results are returned in the Athena console or can be output to a specified S3 bucket for further analysis.
    - Athena executes SQL queries in parallel across distributed compute resources, providing fast query results, even over large datasets
  - <b>Athena vs. Other AWS Services:</b>
    - <b>Athena vs. Amazon Redshift:</b> While Redshift is a managed data warehouse with clustering and indexing for high-performance analytical queries, Athena is more suited for ad-hoc querying of unstructured or semi-structured data directly in S3, without requiring a data warehouse.
    - <b> Athena vs. AWS Glue:</b> Athena is for querying data, while Glue is a fully managed ETL (Extract, Transform, Load) service for preparing and transforming data. Glue can be used to prepare data that Athena can then query.
  - <b>Common Use Cases:</b>
    - Log Analysis: Analyze logs stored in S3 (e.g., CloudTrail, application logs) without moving them into a database.
    - Ad-hoc Data Exploration: Quickly explore and query large datasets in S3 without the need for an ETL process.
    - Data Lake Analytics: Run SQL queries on data stored in your data lake (S3) to derive insights or generate reports.
    - Business Intelligence: Integrate Athena with tools like Amazon QuickSight or other BI tools for data visualization and reporting.
  - Athena Federated Query
    - Multiple Data Sources: Athena Federated Query enables you to run SQL queries across multiple data sources, including on-premises databases, Amazon RDS, Amazon Redshift, Aurora, and third-party databases.
    - Unified Querying: With federated querying, you can combine results from different data sources without needing to consolidate the data manually.
  - Built-in Query History and Monitoring
    - Query History: Athena maintains a history of queries in the console, allowing you to track past queries, rerun them, or optimize them.
    - Amazon CloudWatch Integration: You can monitor query performance and execution metrics using Amazon CloudWatch, which helps identify issues like long-running queries or failed executions.
  -  Fine-Grained Security with AWS IAM and Lake Formation
    - Access Control: Athena integrates with AWS Identity and Access Management (IAM) to manage fine-grained permissions. You can control access to specific databases, tables, and columns using IAM roles and policies.
    - Lake Formation Integration: Athena also integrates with AWS Lake Formation for more advanced, fine-grained access control at the row or column level, ensuring security for sensitive data.
    - Data Encryption: You can query encrypted data in S3 and use encryption options for data at rest and in transit, such as SSE-S3 or SSE-KMS.
  - Wide Data Format Support: Athena supports querying data in various formats, such as:
    - Structured: CSV, TSV
    - Semi-structured: JSON
    - Columnar: Parquet, ORC, Avro
    - Compressed data: Gzip, Snappy, Zlib
  - Query Caching for Repeated Queries
    - Query Results Caching: Athena caches the results of your queries, which allows repeated queries to return faster and reduces the cost, as it avoids rescanning the data. This is particularly useful when you frequently run the same query multiple times.
    - Automatic Reuse of Query Results: Athena automatically returns cached results when you run identical queries, provided the underlying data hasn’t changed.
  - UDF (User-Defined Functions) Support
    - User-Defined Functions: Athena supports UDFs, which allow you to define custom functions in SQL to apply complex logic or transformations to your data during queries.
    - Custom Logic: You can use JavaScript-based UDFs to extend Athena's functionality for complex data processing within SQL queries.
  - Geospatial Queries with Athena
    - This is particularly useful for use cases involving location data, such as mapping, distance calculations, or geofencing.
  - Support for Amazon CloudFront Logs and VPC Flow Logs 
    - Log Analysis: Athena is optimized for querying and analyzing Amazon CloudFront logs, Elastic Load Balancer logs, VPC flow logs, and AWS service logs stored in S3. This makes it an ideal tool for security, compliance, and operational monitoring.
    - Log Queries: You can quickly analyze terabytes of logs to extract insights, troubleshoot issues, or monitor user behavior.
  - Integration with AWS Step Functions
    - Workflow Automation: Athena queries can be orchestrated as part of workflows with AWS Step Functions. This is particularly useful for automating multi-step data analysis processes, such as data ingestion, transformation, and querying.
  - Customizable Timeouts
    - You can set query timeouts to control how long Athena queries run. This is helpful for managing long-running queries and ensuring cost control by terminating inefficient queries.
  - Athena Workgroups
    - Workgroup Management: Athena provides workgroups that help you organize, isolate, and manage queries within a team or organization. You can set policies and monitor query usage per workgroup.
    - Cost Tracking and Limits: Each workgroup can have separate configurations, query limits, and budgets, helping manage costs and resources more efficiently.
  - Parallel Query Execution
    - Parallelism: Athena allows queries to be executed in parallel across multiple compute nodes, which enhances performance, especially for complex or large queries.
    - Distributed Execution: This ensures that large datasets stored across multiple S3 buckets or partitions can be queried efficiently.
  - Athena Query Federation with Lambda UDFs
    - Lambda UDF for Custom Logic: In addition to SQL UDFs, Athena supports using AWS Lambda functions as UDFs (User Defined Functions). This allows you to execute custom code during query execution, enabling more complex logic like calling external APIs, applying machine learning models, or working with unstructured data.
    - Data Transformation on the Fly: These federated queries enable custom processing and transformations that go beyond Athena's built-in SQL functionality.
  - MSCK REPAIR TABLE
    - The MSCK REPAIR TABLE command in Amazon Athena is used to update the AWS Glue Data Catalog with information about new partitions that have been added to a table in Amazon S3, but are not yet reflected in the Data Catalog. This command is particularly useful for partitioned tables when new partitions are manually added to the underlying S3 storage, but the metadata for those partitions has not yet been updated in the Glue Data Catalog.
    - Use it when To avoid manually adding each new partition to the Glue Data Catalog using ALTER TABLE ADD PARTITION.
</details>

<details>
  <summary><b>Athena workgroups?</b></summary>

  - Amazon Athena Workgroups are a powerful feature that allows users to organize, isolate, and manage queries and query results within Athena. Workgroups provide fine-grained control over how queries are executed, monitored, and billed, helping with governance, cost management, and operational efficiency. Let’s break down the details of Athena Workgroups:
  - Each workgroup can have its own 
    - Query History
    - Data limit
    - Im policy 
    - Encryption Setting 
  - Purpose of Workgroups
    - Segregate Queries: Isolate different workloads or users into separate workgroups. This is useful for ensuring that different departments or teams do not interfere with each other’s queries and resource usage.
    - Cost Control: You can track and limit query costs per workgroup, preventing budget overruns by applying query limits, such as the amount of data scanned or the total cost per query.
    - Performance and Usage Monitoring: Workgroups provide detailed monitoring of query performance, making it easier to track query execution times, data scanned, and query results at a granular level.
    - Enforce data governance policies by controlling which data sources are queried and how results are saved.
  - <b> Best Practices for Athena Workgroups</b>
    - Separate Critical Workloads: Use workgroups to separate business-critical workloads (e.g., production) from exploratory workloads (e.g., development or data science). This ensures that heavy exploratory queries do not interfere with critical queries.
    - Monitor Usage Proactively: Set up CloudWatch alarms to monitor usage metrics like data scanned and query failures, and ensure workgroups do not exceed their cost or data limits.
    - Use Workgroups for Cost Allocation: If you need to allocate Athena costs across multiple departments or projects, set up a workgroup for each and track usage separately in AWS Cost Explorer or Athena’s built-in reporting.
    - Configure Encryption Policies per Workgroup: Ensure that sensitive workloads have the appropriate encryption settings (e.g., SSE-KMS) enforced at the workgroup level to protect query results.
  
</details>
<details>
  <summary><b>Athena Best practices?</b></summary>

  1. Optimize Data for Efficient Querying
    - Use Partitioning
      - Partitioning your data in S3 enables Athena to scan only the relevant subset of data, improving query performance and reducing the amount of data scanned.
      - Partition your data based on frequently queried fields, such as date or region
      - If your dataset includes a timestamp column, partition the data by year, month, and day. This ensures that queries only scan the necessary partitions (e.g., a specific date range).
    - Use Columnar Storage Formats
      - Store data in efficient columnar formats like Parquet or ORC. These formats allow Athena to read only the columns it needs, reducing the amount of data scanned.
    - Compress Your Data
      - Compressing your data reduces storage costs and improves query performance by reducing the amount of data read from S3.
      - Apply compression algorithms like Snappy, Gzip, or Zlib to data stored in S3.
    - Reduce Small Files
      - Athena performs better when querying larger files rather than many small files, which can cause overhead and increase query time.
      - Combine smaller files into larger files during your ETL process.
  2. Optimize Query Performance
    - Select Specific Columns Instead of Using SELECT *
      - Query only the columns you need to reduce the amount of data scanned.
    - Use LIMIT for Quick Query Validation
      - When testing or validating queries, use the LIMIT clause to quickly return a subset of results without scanning the entire dataset.
    - Use Proper Filtering (WHERE Clauses)
      - Narrow down your queries by using WHERE clauses to filter data, which helps reduce the amount of data scanned.
      - Apply filters based on indexed or partitioned columns.
    - Optimize Join Operations
      - Minimize the data used in joins by filtering the tables before joining them.
      - Use WHERE or ON conditions to reduce the size of the datasets before performing the join.
      - SELECT * FROM orders o JOIN customers c ON o.customer_id = c.customer_id WHERE o.region = 'West' to limit the join to only relevant rows.
  3. Cost Optimization
    -  Reduce Data Scanned by Partitioning and Columnar Storage
      - Partitioning and columnar storage formats (like Parquet or ORC) minimize the amount of data scanned, directly reducing costs.
    - Use Data Lake Optimization Techniques
      - Use AWS Glue to merge smaller files into larger ones, and convert them into columnar formats with compression.
    - Monitor Query Costs
      - Use Athena Cost and Usage Reports to track and manage query costs.
      - Analyze Athena query costs using AWS Cost Explorer and set up billing alerts.
  4. Security Best Practices
    - Use Fine-Grained Access Control
      - Limit access to data and queries based on users' roles and responsibilities using AWS IAM and AWS Lake Formation for fine-grained permissions.
    - Encrypt Data at Rest and In Transit
      - Use SSE-S3 or SSE-KMS to encrypt data stored in S3, and require SSL/TLS for Athena queries.
    - Enable Logging and Auditing
      - Enable logging of Athena query activity using AWS CloudTrail for auditing purposes and Amazon CloudWatch for monitoring.
      - Track Athena API calls with CloudTrail, and monitor query execution times and errors with CloudWatch.
  5. Data Management Best Practices
    - Leverage the AWS Glue Data Catalog
      - Use the AWS Glue Data Catalog as a central metadata repository for Athena. This makes it easier to manage schemas and ensures consistency.
    - Implement Data Lifecycle Management
      - Use S3 lifecycle policies to automatically transition or delete old data that is no longer needed.
  6. Query and Result Management
    - Use the Query History for Optimization
      - Review the query history in Athena to identify long-running or expensive queries and optimize them.
      - Use the Athena console to view and analyze past query execution times and costs.
      - Identify a frequently run query that scans too much data, and optimize it by adding partition filters or using columnar storage.
    - Manage Query Results Efficiently
      - Athena stores query results in S3, which can incur additional storage costs. Regularly clean up unused query results.
</details>
<details>
  <summary><b>Athena ACID Transaction</b></summary>

  - Amazon Athena now supports ACID (Atomicity, Consistency, Isolation, Durability) transactions for Apache Iceberg, a popular table format for managing large datasets in data lakes. ACID transactions ensure that your data remains consistent and reliable during query execution, updates, and deletes. This capability enhances Athena’s ability to handle complex data processing tasks, making it a powerful tool for running transactional queries on a data lake.
  - <b> Key Features of ACID Transactions in Athena</b>
    - Atomic INSERT, UPDATE, DELETE Operations:
      - You can run SQL operations like INSERT, UPDATE, DELETE on large datasets using ACID compliance to ensure the changes are made atomically.
      - This is crucial when you need to modify data without risking partial updates, which could leave your data in an inconsistent state.
    - Transactional Consistency:
      - When modifying data, all changes within a transaction are applied together, preventing inconsistencies that could arise from partial updates.
      - If a transaction fails, all changes are rolled back, ensuring the integrity of the data.
    - Isolation for Concurrent Queries:
      - ACID transactions allow multiple users or applications to perform read and write operations concurrently without affecting each other’s work.
      - Readers will only see the fully committed changes from a transaction, while writers can continue modifying data without waiting for readers to complete their queries.
    - Time Travel and Snapshot Isolation
      - With Apache Iceberg, Athena supports time travel queries, allowing users to query historical versions of a table. This is done using table snapshots that capture the state of the data at different points in time.
      - You can restore or query data as it was at a particular point, which is valuable for debugging, auditing, or analyzing historical data.
    - Schema Evolution:
      - Athena supports schema evolution with ACID transactions, meaning that you can change the schema of an Iceberg table (e.g., add columns) without breaking existing queries.
      - Changes to the schema are tracked in metadata, and queries can adapt to those changes seamlessly.
    - Efficient Query Performance with Partitioning:
      - Iceberg supports partitioning, and Athena can query partitioned datasets efficiently, skipping unnecessary files or data during query execution. This leads to faster query performance on large datasets.
  - <b> How ACID Transactions Work in Athena with Iceberg? </b>
    - Create a Table: You can create a table using the Iceberg format in Athena. For example:
      ```
      CREATE TABLE my_iceberg_table (
          id INT,
          name STRING,
          value DOUBLE
      )
      PARTITIONED BY (date STRING)
      STORED AS ICEBERG;
      ```
    - Insert Data: Use the INSERT INTO statement to add data. With ACID support, this ensures that either all the data is added, or none is, in case of failure.
      ```
      INSERT INTO my_iceberg_table (id, name, value, date)
      VALUES (1, 'example', 100.0, '2024-10-18');

      ```
    - Update or Delete Data: You can perform transactional updates and deletes using the UPDATE and DELETE statements:
      ```
      -- Update specific rows
      UPDATE my_iceberg_table
      SET value = 200.0
      WHERE id = 1;

      -- Delete specific rows
      DELETE FROM my_iceberg_table
      WHERE id = 1;

      ```
    - Commit and Rollback:
      - Once changes are made in a transaction, they are committed to the table, ensuring they are visible to other queries.
      - In case of an error during a transaction, Athena will automatically roll back the changes, ensuring that no partial updates are visible.
    - Time Travel Queries: With Iceberg, you can query historical data by specifying a snapshot:
      ```
      SELECT * FROM my_iceberg_table FOR SYSTEM_TIME AS OF '2024-10-10T00:00:00Z';
      ```
    - We Need to define bucket to store table data and meta data of table
      ```
      CREATE TABLE my_iceberg_table (
          id INT,
          name STRING,
          value DOUBLE,
          event_time TIMESTAMP
      )
      PARTITIONED BY (event_time) -- partition by time or another column
      STORED AS ICEBERG
      LOCATION 's3://your-bucket/iceberg/my_iceberg_table/';

      ```
    - OPTIMIZE table REWRITE DATA  USING BIN_PACK  WHERE catalog = 'c1'
      - REWRITE DATA
        - This part tells Iceberg to rewrite the data files within the table. The purpose is to reduce the number of small files or poorly organized data, which can slow down queries.
      - USING BIN_PACK:
        - BIN_PACK is a file compaction strategy in Iceberg. It groups smaller files into larger bins (files), aiming to create larger, more efficient files. This reduces the number of files that need to be scanned during queries, leading to better performance.
        - Other strategies could include Z-order or sort-based optimization, but BIN_PACK is typically used to compact small files efficiently.
      - OPTIMIZE my_table_name:
        - The command starts by specifying the Iceberg table (my_table_name) that you want to optimize. The optimization process is carried out to improve performance by compacting small files into fewer, larger files.
</details> 