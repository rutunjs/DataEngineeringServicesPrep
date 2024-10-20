## AWS ICE burg
<details>
  <summary><b>What is ICE burg</b></summary>

  - Apache Iceberg is an open table format designed for managing large datasets in data lakes. It provides advanced data management capabilities, such as <b>ACID transactions, schema evolution, and efficient querying over massive data stored in formats like Parquet, ORC, or Avro.</b> Iceberg is built for distributed processing frameworks like Apache Spark, Flink, and query engines like Presto, Trino, and Amazon Athena. It addresses the challenges of managing petabyte-scale data by offering better performance, version control, and flexibility for data lakes.
  - <b>Key Features</b>
    - ACID Transactions:
      - Iceberg supports Atomicity, Consistency, Isolation, and Durability (ACID) transactions, ensuring reliable data ingestion and update processes.
      - This means you can perform inserts, updates, and deletes safely, without worrying about data corruption or inconsistencies during concurrent operations.
    - Schema Evolution:
      - Iceberg allows you to evolve your table schema (e.g., adding, dropping, renaming, or updating columns) without impacting queries on older data.
      - It manages schema changes in a way that maintains data compatibility across different versions of the table.
    - Time Travel:
      - Iceberg supports time travel queries, which means you can query data as it existed at a particular point in time.
      - This is useful for auditing, debugging, or comparing historical datasets.
    - Efficient Query Performance:
      - Iceberg optimizes data layout and query performance with features like partition pruning, file pruning, and metadata caching.
      - It eliminates the common “small files” problem in data lakes by organizing data into fewer, larger files and tracking the table's layout using metadata.
    - Partitioning and Partition Evolution:
      - Iceberg offers flexible partitioning, enabling you to define and modify partitions without needing to rewrite the entire dataset.
      - It uses a hidden partitioning mechanism, so the partition logic is abstracted from users, making queries simpler and partition pruning more efficient.
    - Versioned Data and Snapshots:
      - Iceberg tracks table versions and snapshots, allowing you to roll back to earlier versions of a dataset or perform incremental processing based on changes since a certain snapshot.
      - Snapshots are immutable and help maintain consistency across different consumers of the data.
    - Support for Multiple Query Engines:
      - Iceberg is engine-agnostic and integrates with various query engines like Apache Spark, Presto, Trino, Flink, and Amazon Athena.
    - Data Layout Optimizations:
      - Iceberg optimizes how data is laid out on disk by supporting techniques like bin-packing and Z-order clustering to organize data files for optimal scanning.
      - This ensures that queries read the minimum amount of data needed, improving efficiency.
    - Support for Incremental Data Processing:
      - Iceberg enables incremental data ingestion and processing, allowing users to efficiently load new data or process only updated or inserted records.
      - This feature is particularly useful for ETL (Extract, Transform, Load) and real-time analytics use cases.
  - <b>Architechture </b>
    - Catalogs - Iceberg supports various catalogs (like AWS Glue, Hive Metastore, or Nessie) for tracking table metadata.
    - Metadata Layer: Iceberg maintains metadata about the table, partitions, and data files. This metadata is stored separately and enables faster querying by tracking the physical layout of the data.
    - Manifest Files: Iceberg uses manifest files to track data files and partitions. These manifests allow query engines to know exactly which files to read for any given query.
    - Snapshots: A table's state is defined by a snapshot, which records the exact set of data files that make up the current version of the table.
  - <b>Use Case </b>
    - Data Lakes:Iceberg is ideal for building robust data lakes, allowing users to manage huge datasets efficiently while supporting transactional capabilities and data versioning.
    - Streaming and Batch Data Processing: Iceberg works well with both batch and streaming workloads. It supports incremental data processing, making it suitable for real-time analytics.
    - Data Warehousing and BI: Iceberg’s ability to handle large, evolving datasets makes it useful for modern data warehousing and business intelligence (BI) use cases.
    - Data Governance and Auditing: With features like time travel, schema evolution, and ACID transactions, Iceberg supports data governance and auditing requirements in regulated industries.

</details>

<details>
  <summary><b>What is Hidden partitioning?</b></summary>
  
  - Hidden partitioning in Apache Iceberg is a feature that abstracts away the complexities of traditional partitioning from the user, while still allowing the system to benefit from the performance improvements of partition pruning. In traditional data lake solutions, users must explicitly define and manage partitions, which can lead to challenges such as complex partition schemes, maintenance issues, or improper query optimization. Iceberg's hidden partitioning simplifies this by automatically managing partitioning without exposing the implementation details to users.
  - Automatic Partitioning:
    - In Iceberg, users don't need to manually define partition columns or deal with the physical layout of partitions. Instead, Iceberg automatically partitions the data in the background using the columns that the user queries most often.
    - This removes the need for users to think about partition strategies and still gain the benefits of optimized querying.
  - Partition Pruning:
    - While partitioning is hidden from the user, Iceberg still ensures that queries only scan the relevant partitions by applying partition pruning behind the scenes.
    - When a query is run, Iceberg checks the table metadata to identify which partitions contain the data of interest. It avoids reading unnecessary partitions, improving query performance and reducing cost.
  - No Manual Directory Structure:
    - Unlike traditional partitioned tables (e.g., in Hive or older data lake architectures), where the data is stored in directories like /year=2023/month=10/, Iceberg handles partitioning purely in metadata.
    - This avoids common issues with small files and inefficient directory structures, and makes schema evolution easier, as Iceberg can manage changes to partitions automatically.
  - Derived and Transformed Partitions:
    - Iceberg supports derived partitions, which means the actual partitioning scheme is based on transformations of columns rather than the raw column values.
    - For example, instead of storing dates as year=2023/month=10, Iceberg can internally use transformations such as date_trunc('month', timestamp) for more efficient querying.
    - The partitioning strategy is stored in the metadata of the table, so Iceberg can transform the values back to their original form when queried, without the user being aware of the transformation.
  - Dynamic Partitioning:
    - Iceberg can dynamically adjust its partitioning strategy as data is added or queried. It may repartition data based on how it’s being used, optimizing for performance over time.
    - This adaptability removes the need for users to rethink or reconfigure partition strategies as data grows or query patterns change.
  - <b>Advantages</b>
    - Automatic Optimization
    - Easier Schema Evolution
    - Faster Query Performance
    - No Need for Manual Partition Repair
  
  - Comparison with Traditional Partitioning

  <table>
    <tr>
      <th>Traditional Partitioning</th>
      <th>Iceberg Hidden Partitioning</th>
    </tr>
    <tr>
      <td>Users must manually define and manage partitions (e.g., year, month).</td>
      <td>Iceberg automatically handles partitioning based on how the data is queried.</td>
    </tr>
    <tr>
      <td>Partitioning logic is tied to directory structures (e.g., s3://bucket/year=2023/month=10/).</td>
      <td>No need for explicit directory structures. Partitioning is handled in metadata.</td>
    </tr>
    <tr>
      <td>Schema evolution can be difficult if the partitioning structure needs to change.</td>
      <td>Iceberg allows schema evolution without the need to reorganize data.</td>
    </tr>
    <tr>
      <td>Requires manual metadata updates (e.g., MSCK REPAIR TABLE).</td>
      <td>Iceberg automatically tracks partitions in its metadata.</td>
    </tr>
    <tr>
      <td>Limited ability to handle changes in query patterns or data growth.</td>
      <td>Iceberg can dynamically adjust partitioning based on usage and data growth.</td>
    </tr>
  </table>
</details>

<details>
  <summary><b>ICEburg Queries</b></summary>

  - DDL (Data Definition Language) Queries
    - CREATE TABLE
      - Creates a new Iceberg table with specified schema and partitioning
        ```
        CREATE TABLE sales (
          id BIGINT,
          amount DECIMAL(10, 2),
          date DATE
        )
        USING iceberg
        PARTITIONED BY (date);

        # Using select statement
        CREATE TABLE new_sales AS
          SELECT id, amount, date
          FROM old_sales
          WHERE amount > 100;

        ```
    - ALTER TABLE (Add/Drop Columns, Change Column Type)
      - Modifies the schema of an Iceberg table by adding, dropping, or altering column
        ```
        ALTER TABLE sales ADD COLUMN region STRING;
        ALTER TABLE sales DROP COLUMN old_column;
        ALTER TABLE sales RENAME COLUMN old_column TO new_column;

        # Modify or add partitioning to an existing Iceberg table.
        ALTER TABLE sales SET PARTITION SPEC (bucket(id, 10));
        ```
    - DROP TABLE
      - Deletes an Iceberg table and its metadata.
        ```
        DROP TABLE sales;
        ```
    - RENAME TABLE
      ```
      ALTER TABLE sales RENAME TO sales_archive;
      ```
    - SHOW PARTITIONS
      ```
      SHOW PARTITIONS FROM sales;
      ```
    - DESCRIBE TABLE
      - Shows the schema of the Iceberg table, including column names and type
        ```
        DESCRIBE sales;
        ```
    - MSCK REPAIR TABLE (in Athena or Hive Metastore)
      - Updates the partition metadata of an Iceberg table in the Glue Data Catalog.
        ```
        MSCK REPAIR TABLE sales;
        ```
  - DML (Data Manipulation Language) Queries
    - INSERT INTO (Append Data)
      ```
      INSERT INTO sales VALUES (1, 100.50, '2023-10-18');

      # insert as select
      INSERT INTO sales SELECT id, amount, date FROM transactions WHERE date >= '2023-01-01';

      #Replaces the data in a table or specific partitions.
      INSERT OVERWRITE sales SELECT id, amount, date FROM new_sales;

      ```
    - UPDATE
      ```
      UPDATE sales SET amount = amount * 1.1 WHERE region = 'North America';
      ```
    - DELETE
      ```
      DELETE FROM sales WHERE date < '2022-01-01';
      ```
    - MERGE INTO
      - Combines updates, inserts, and deletes based on matching conditions between two tables.
        ```
        MERGE INTO sales
        USING new_sales
        ON sales.id = new_sales.id
        WHEN MATCHED THEN UPDATE SET sales.amount = new_sales.amount
        WHEN NOT MATCHED THEN INSERT (id, amount, date) VALUES (new_sales.id, new_sales.amount, new_sales.date);
        ```
    - SELECT (Time Travel and Versioned Queries)
      - Query data from specific versions or snapshots of an Iceberg table
        ```
        SELECT * FROM table_name SNAPSHOT snapshot_id;
        SELECT * FROM sales SNAPSHOT 1234567890123;

        SELECT * FROM table_name AS OF timestamp;

        # expire snapshot
        CALL system.expire_snapshots('sales', older_than => '2023-01-01');
        ```
    - Optimize Table (Compaction)
      - Optimize file sizes or reorganize data for better query performance.
        ```
        OPTIMIZE sales REWRITE DATA USING BIN_PACK;
        ```

</details>

<details>
  <summary><b>Best practices ICEburg</b></summary>


  - Data Partitioning Strategy
    - Choose optimal partitioning strategies based on query patterns rather than data ingestion patterns.
    - Use Iceberg’s hidden partitioning feature to abstract partition management and let Iceberg handle partition optimizations based on metadata.
    - Avoid over-partitioning (e.g., day-level partitions for high-frequency data) to reduce the number of small files, which can degrade performance.
      ```
      CREATE TABLE sales USING iceberg PARTITIONED BY (date_trunc('month', sales_date));
      ```
  - Optimize Table Regularly
    - Regularly compact small files into larger files using Iceberg's OPTIMIZE commands or scheduling optimizations during periods of low activity.
    - Use bin-packing strategies for file compaction to reduce the number of files per partition and improve query performance.
    - Why: Iceberg performs best when the number of small files is minimized. Compacting data files improves query speed, as fewer files need to be read.
      ```
      OPTIMIZE sales REWRITE DATA USING BIN_PACK;
      ```
  - Expire Old Snapshots and Metadata
    - Expire older snapshots periodically to free up storage space and reduce metadata overhead.
    - Define a retention policy based on business requirements to determine how long old snapshots should be kept.
    - Why: Iceberg keeps track of all snapshots for time travel and rollback purposes, which can accumulate and increase storage and metadata size. Expiring old snapshots ensures efficient use of resources.
      ```
      CALL system.expire_snapshots(table => 'sales', older_than => '2023-01-01');
      ```
  - Use Time Travel with Caution
    - Leverage time travel queries for audits, debugging, or compliance, but avoid excessive use in regular production workflows to prevent performance overhead.
    - Why: Time travel is a powerful feature that allows you to query historical data at specific points, but querying large historical snapshots can lead to expensive scans.
    ```
    SELECT * FROM sales AS OF '2023-01-01T00:00:00';
    ```
  - Monitor Query Performance
    - Continuously monitor query performance to identify any degradation, especially after schema changes or as data volume grows.
    - Use partition pruning and metadata caching features to improve query performance, ensuring that only relevant data files are scanned.
    - Why: Performance may degrade over time if partitions, files, and metadata are not well-maintained. Monitoring allows you to proactively address issues
  - Schema Evolution and Management
    - Utilize Iceberg’s schema evolution features (e.g., adding, removing, renaming columns) with caution, ensuring changes are backward compatible.
    - Avoid dropping columns unless absolutely necessary, as it may impact queries or ETL jobs using older versions of the schema.
    - Why: Schema evolution allows changes to the dataset without rewriting historical data, but it’s important to test changes to ensure compatibility with older versions.
      ```
      ALTER TABLE sales ADD COLUMN region STRING;
      ALTER TABLE sales RENAME COLUMN old_name TO new_name;
      ```
  - Efficient Metadata Management
    - Iceberg maintains metadata in manifest files, so ensure that metadata grows efficiently by regularly compacting manifests and avoiding the creation of excessive metadata for small datasets.
    - Why: Excessive or large metadata can slow down query performance, especially when manifest files become too large.
    - Action: Run optimizations periodically, use metadata compaction features, and avoid generating too many small files or tables.
  - Use Partition and File Pruning
    - Ensure partition pruning is happening by querying based on partition columns and use file pruning to limit the number of files read during scans.
    - Why: Partition and file pruning reduce the number of files and partitions scanned, leading to more efficient queries. Iceberg’s metadata-driven design optimizes this process, but queries need to take advantage of it.
    ```
    SELECT * FROM sales WHERE sales_date >= '2023-01-01';
    ```
  - Batch Write and Streaming Ingest
    - Use batch writes for large data ingestion to reduce file fragmentation. When streaming data, leverage Iceberg’s support for incremental updates to avoid writing many small files.
    - For streaming use cases, tune the checkpoint interval and batch size to balance performance with real-time requirements.
    - Why: Writing too many small files from streaming jobs can hurt query performance and lead to excessive file management overhead.
  - Governance and Access Control
    - Implement proper data governance using services like AWS Lake Formation or Hive Metastore for fine-grained access control.
    - Leverage Iceberg’s integration with catalog services to manage permissions, audits, and compliance, ensuring that users only access the necessary data.
    - Why: As datasets grow, ensuring security and governance compliance becomes crucial. Centralized governance allows better control over who can query, modify, or manage the data.
  - ACID Transactions and Consistency
    - Iceberg supports ACID transactions, so use this feature for concurrent data updates, inserts, and deletes.
    - Batch small changes together to ensure transactional integrity without introducing overhead.
    - Why: Iceberg's support for ACID ensures that the data remains consistent even with concurrent reads and writes, avoiding issues like dirty reads or partial writes.
  - Minimize Data Duplication in ETL Processes
    - Use MERGE INTO queries or incremental processing in your ETL pipelines to ensure that only new or changed data is processed and ingested.
    - Regularly deduplicate data at the source to avoid introducing multiple copies of the same record, which can bloat the data lake.
    - Why: Minimizing duplicates and redundant data processing reduces storage costs and improves query performance
      ```
      MERGE INTO sales USING new_data
      ON sales.id = new_data.id
      WHEN MATCHED THEN UPDATE SET sales.amount = new_data.amount
      WHEN NOT MATCHED THEN INSERT (id, amount, date) VALUES (new_data.id, new_data.amount, new_data.date);
      ```
  - Backup and Disaster Recovery
    - Ensure that backup and recovery plans are in place. Iceberg’s snapshot feature can act as a form of backup, but you should have a strategy for replicating data to another region or storage service.
    - Why: As with any large-scale system, having backup and disaster recovery in place is critical to avoid data loss during unexpected failures.
</details>