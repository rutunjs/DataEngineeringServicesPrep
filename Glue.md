## AWS GLUE 
<details>
  <summary><b>What is Glue?</b></summary>

  - Glue is a fully managed ETL service that simplifies the process of preparing and transforming data for analytics. It automates data discovery, schema inference and job scheduling and allowing to organizing to efficiently manage data workflow.
  - Serverless discovery of data defination and schema 
  - Supports Database as source system 
    - RDS, Redshift, S3 Data lake, Dynamo DB , MongoDB, other SQL databases , ICE burg
    - We can connect with JDBC/ ODBC connections
      - Need to create connection for that with database details and drivers
      - We can specify drivers class in connection and also upload drivers in S3 bucket and give path in connection
      - IF SSL enable then we need to Provide certification path from S3 or we can skip validation as well
  - Custom ETL (Extract, Transform, Load) job in AWS Glue refers to creating a more specialized and tailored ETL workflow beyond Glue's built-in transformations, often using Python or Scala scripts to perform specific data transformations that are not covered by the default Glue features. AWS Glue is a serverless ETL service that automates much of the ETL process, but in some cases, the built-in functionality may not suffice, leading to the need for custom ETL jobs.
  - Glue Supported file format 
    - CSV, JSON, Parqute, ORC, Excel, Avro, XML, Text file, Gzip and bzip compressed file
  - We can create GLue note books and create job that tun ETL
  - We can create VPC endpoint and can connect it to 
    - Zeppline in local machine 
    - Sagemaker notebook
    - Terminal 
    - Use Elastic Ip for private endpoint address
  - <b> Limitation </b>
    - Nested Schema are not supported 
    - Takes time to scan large dataset
    - Not all files format are supported 
    - Not able to crwal all tables at once there is limit of 50 tables that can crawl simulateneously 
    - If you want to use other engines (Hive,Pig, etc) Data Pipeline EMR would be a better fit.


</details>

<details>
  <summary><b>What is Glue Crwaler and Catalog?</b></summary>

  - <b> Crawler </b> 
    - Automatically scan data source and extracts metadata such as table name, column name and data types
    - It can scans data in S3, creates schema for that
    - It can run Periodically 
    - It can run On demand as well
    - Subsequant crawler run
      - Crawl all sub folder
      - crawl new subfolder only 
      - based on event in s3 bucket
    - We can integrate it with Lake formation
    - Once Data got crwal we can query it in structure menner using Athena
    - It can be accessible from various sources like Athena, Redshift Spectrum, EMR and Quick Sight
    - How it works ? 
      - Crwaler connect to data source like S3, or any other database and infer schema and create or update tables in Glue data catalog
    - It will partition data based on folder structure in S3
      - like year=2013/month=12
      - Needs to follow partition based on frequently access data if date use more then use it as partition because that minimize scaning of file and it reduce data cost
      - It's mendataory to think how we will query 
    - <b>Note : </b>
      - If we add some files in same partition which was already crwal then file will be automatically populate
      - But if partition is created after crwal then we need to run crwaler again to populate that data 
  - <b> Data Catalog</b>
    - It is centralize repository that store <b>metadata</b> about data source
    - It will be populated by Crwalers
    - It can be serve As hive meta store in EMR cluster by setting up hive.metastore.uris=thrift://glue-endpoint:9000
  - Steps to follow 
    - Go to AWS crwaler and create new crwaler
    - Run That crwaler 
    - It will create metadataor update tables in data catalog with extrected metadata
    - Tables will be visible in Data catalog in AWS console
    - Now we can directly use this data catalog for ETL purpose 
  - How to handle Schema drift in Crwaler
    - Update metadata of that column
    - Add new column but not overwrite existing column
    - Ignor changes and don't update 
  - We can use Lambda to create Glue crwaler in dynamic way using SDK 
  - We can use CICD pipline to Pull data from github and then build stage and deploy it to Lambda
  - We need to trigger lambda function after deployment from stages to deploy crwaler 
  - <b> Security Practicce </b>
    - Use IAM roles for and polices to control access to aws resources. Ensure that only authorize user have access for sensitive data
    - Enabble data at rest encryption
    - Monitor with Cloudwatch 
    - Implement VPC endpoint
</details>

<details>
  <summary><b> What is Glue ETL?</b></summary>

  - <b> Key Features </b>
    - Serverless
    - Data catalog 
    - Automatic Code generation
    - Glue Studio provide a graphical interface for developing ETL jobs
    - Job Scheduling 
    - Broad data source support
    - Scalability - Auto scale resource to handle anysize of data processing
    - Transfomration - Provide built in transofrmation to clean, transform and enrich data
    - Job Monitoring - Aws cloudwathch for detail monitoring 
    - Security - Support data at rest and in Transist, IAM for finegrain access control, VPC endpoint for 
    secure data transfer
    - Can be event driven - Call it from Lambda or Step functions Cloudwatch event, S3 event,  CLI or SDK 
    - Use Cloudwatch events to identify success or failed job and trigger sns notification 
    - <b>Streming </b>
      - Can consume directly from Kinesis or Kafka just we need to define kinesis and stream name
      - Clean & transform in-flight
      - Store results into S3 or other data stores
  - Support Python and Scala
  - Can provision additional "DPU's" (data processing units) to increase performance of underlying Spark jobs
    - Job metrics can help us to understand maximum capacity of DPU needs
  - Errors will shown on Cloudwatch and we can trigger event to SNS or as per needs
  - We can schedual job periodically 
  - it's fully manage cost effective and pay as you go model 
  - <b>Important Parameters</b> 
    - JOB_NAME - Need to give job name
    - TempDir - Specifies the temporary directory in Amazon S3 where Glue stores intermediate job results.
    - --region - Where job will run 
    - --job-bookmark-option - Controls whether to use AWS Glue job bookmarks, which track the progress of the job and allow incremental loading of data.
      - Values: "job-bookmark-enable", "job-bookmark-disable", "job-bookmark-pause"
    - --enable-metrics - Enables publishing of metrics to AWS CloudWatch.
    - --timeout - The maximum time (in minutes) that the job should be allowed to run before being stopped.
    - --MaxCapacity - The maximum number of AWS Glue data processing units (DPUs) to allocate to the job.
    - --worker-type - "Standard", "G.1X", "G.2X"
    - --number-of-workers - Specifies the number of workers allocated for the job.
    - --scriptLocation - "s3://my-scripts-bucket/scripts/my-etl-script.py"
    - Glue Specific Attribute
      - --extra-py-files - Specifies additional Python files that are required for the ETL job. This is useful if you have custom libraries.
      - --extra-jars - "s3://my-bucket/jars/my-custom-jar.jar"
    - --extra-file - Specifies additional files that should be passed to the Glue job, such as configuration files.
      - Example: "s3://my-bucket/config/config.json"
    - --enable-continuous-log-filtering - nables continuous log filtering, so logs are streamed to CloudWatch as the job runs.
    - --enable-spark-ui -  true or false
    - --spark-event-logs-path - Specifies the S3 path to store Spark event logs.
    - --log-level - "INFO", "ERROR", "WARN", "DEBUG"
  - <b> Script Parameters </b>
    - --args - Allows passing custom arguments to the ETL script. These are accessible within the script.
      - Example: --args '{"key1":"value1", "key2":"value2"}'
    - --enable-glue-datacatalog - Specifies whether to use AWS Glue Data Catalog as the Hive metastore for the Spark job.
    - --connections - Specifies the Glue Data Catalog connections to be used by the job for accessing data in databases.
    - --dynamic-frame-transformations - Enables dynamic frame transformations, which allow for schema evolution and flexible transformations using Glue’s native dynamic frames.
    - --user-jars-first - Indicates whether user-provided JARs should be prioritized over Glue-provided JARs during execution.
  - <b> Data quality Rules </b>
    - Data quality rules may be created manually or recommended automatically
    - Results can be used to fail the job, or just be reported to CloudWatch
      ```
      import sys
      from pyspark.sql import SparkSession
      from awsglue.transforms import *
      from awsglue.utils import getResolvedOptions
      from pyspark.sql.functions import col, isnan

      # Initialize Spark Session
      spark = SparkSession.builder.appName("DataQualityJob").getOrCreate()

      # Extract job arguments (from Glue script parameters)
      args = getResolvedOptions(sys.argv, ['JOB_NAME', 'SOURCE_PATH', 'TARGET_PATH'])

      # Read the input data from S3 using dynamic frame
      source_path = args['SOURCE_PATH']
      input_data = spark.read.format("csv").option("header", "true").load(source_path)

      # Define custom data quality rules (DQDL-like constraints)
      data_quality_rules = {
          "not_null_columns": ["id", "name", "age"],
          "min_value_constraints": {"age": 18},
          "unique_columns": ["id"]
      }

      # Function to check for null values in the specified columns
      def check_not_null(df, columns):
          for col_name in columns:
              null_count = df.filter(col(col_name).isNull() | isnan(col_name)).count()
              if null_count > 0:
                  print(f"Data Quality Issue: {col_name} has {null_count} null values.")
                  raise ValueError(f"Data Quality Check Failed: {col_name} contains null values.")
          return df.dropna(subset=columns)

      # Function to check for minimum value constraints
      def check_min_value(df, min_value_constraints):
          for col_name, min_value in min_value_constraints.items():
              invalid_count = df.filter(col(col_name) < min_value).count()
              if invalid_count > 0:
                  print(f"Data Quality Issue: {col_name} has {invalid_count} values below {min_value}.")
                  raise ValueError(f"Data Quality Check Failed: {col_name} contains values below {min_value}.")
          return df.filter(col(col_name) >= min_value)

      # Function to check for uniqueness constraints
      def check_unique(df, unique_columns):
          for col_name in unique_columns:
              dup_count = df.groupBy(col_name).count().filter(col("count") > 1).count()
              if dup_count > 0:
                  print(f"Data Quality Issue: {col_name} has {dup_count} duplicate values.")
                  raise ValueError(f"Data Quality Check Failed: {col_name} contains duplicate values.")
          return df.dropDuplicates(unique_columns)

      # Apply Data Quality Rules
      try:
          cleaned_data = check_not_null(input_data, data_quality_rules["not_null_columns"])
          cleaned_data = check_min_value(cleaned_data, data_quality_rules["min_value_constraints"])
          cleaned_data = check_unique(cleaned_data, data_quality_rules["unique_columns"])

          # Write the clean data back to S3
          target_path = args['TARGET_PATH']
          cleaned_data.write.mode("overwrite").format("csv").save(target_path)

          print("Data Quality Checks Passed: Job Completed Successfully.")

      except ValueError as e:
          print(f"Job Failed: {e}")
          # If there's an error, throw an exception to fail the Glue job
          raise Exception("Job terminated due to data quality issues.")

      # End the Glue job
      spark.stop()

      ```
</details>

<details>
  <summary><b>Glue ETL Coommands and Sample Data</b></summary>

  - Sample Program 
    - Here we can use getResolveOptions to get job variables
      ```
      import sys
      from awsglue.transforms import *
      from awsglue.utils import getResolvedOptions
      from pyspark.context import SparkContext
      from awsglue.context import GlueContext
      from awsglue.job import Job

      args = getResolvedOptions(sys.argv, ['JOB_NAME'])

      sc = SparkContext()
      glueContext = GlueContext(sc)
      spark = glueContext.spark_session
      job = Job(glueContext)
      job.init(args['JOB_NAME'], args)

      # Reading data from S3
      datasource = glueContext.create_dynamic_frame.from_catalog(database = "my_database", table_name = "my_table")

      # Custom transformation logic
      transformed_data = datasource.toDF().filter("year > 2020").groupBy("region").agg({"sales": "sum"})

      # Writing back to S3
      glueContext.write_dynamic_frame.from_options(
          frame = DynamicFrame.fromDF(transformed_data, glueContext, "transformed_data"),
          connection_type = "s3",
          connection_options = {"path": "s3://my-output-bucket/transformed-data/"},
          format = "parquet"
      )

      job.commit()
      ```
  - <b>Select Fields</b>  - This transformation is used to select specific columns from a DynamicFrame.
    ```python
    from awsglue.transforms import SelectFields
    selected_fields = SelectFields.apply(frame=dyn_frame, paths=["column1", "column2"])
    ```
  - <b>Drop Fields </b> - Removes specified columns from the data.
    ```python
    from awsglue.transforms import DropFields
    dropped_fields = DropFields.apply(frame=dyn_frame, paths=["unnecessary_column"])

    ```
  - <b>Filter </b> - Filters records based on a condition.
    ```python
    from awsglue.transforms import Filter
    filtered_data = Filter.apply(frame=dyn_frame, f=lambda row: row["column"] > 100)

    ```
  - <b>Map </b> - Applies a function to each row in the DynamicFrame, allowing you to perform transformations row-by-row.
    ```python
     mapped_data = dyf.map(f=lambda row: {"new_column": row["old_column"] * 2})
    ```
  - <b>RenameField</b> - Renames a column in the DynamicFrame.
    ```python
    from awsglue.transforms import RenameField
    renamed_fields = RenameField.apply(frame=dyn_frame, old_name="old_column", new_name="new_column")

    ```
  - <b>Join </b> - Joins two DynamicFrames based on specified keys.
    ```python
    from awsglue.transforms import Join
    joined_data = Join.apply(dyn_frame1, dyn_frame2, 'key1', 'key2')

    ```
  - <b>Relationalize</b> - Breaks down nested structures (e.g., arrays, structs) into relational tables
    ```python
    relationalized = dyn_frame.relationalize("root_table", "s3://my-bucket/relational-output/")

    ```
  
  - <b>ApplyMapping</b> - Maps fields from the source DynamicFrame to a new format (e.g., changing column names or types).
    ```python
    from awsglue.transforms import ApplyMapping
    mapped_data = ApplyMapping.apply(frame=dyn_frame, mappings=[("old_col", "string", "new_col", "int")])
    ```
  - <b>Drop Null Fields</b> - Drops rows with null values in the specified fields.
    ```python
    from awsglue.transforms import DropNullFields
    non_null_data = DropNullFields.apply(frame=dyn_frame, transformation_ctx="dropnullfields")

    ```
  - <b>Split Fields</b> - Splits columns with a delimiter into multiple columns.
    ```python
    split_data = dyn_frame.map(f=lambda row: {"column1": row["combined_column"].split(":")[0], "column2": row["combined_column"].split(":")[1]})

    ```
  - <b>Union</b> - Merges two DynamicFrames, concatenating rows from both into one.
    ```python
    combined_data = dyn_frame1.union(dyn_frame2)
    ```
  - <b>Spigot (Sampling)</b> - Collects a sample of records from a DynamicFrame for testing
    ```python
    sampled_data = dyn_frame.spigot("s3://my-sample-output/", prob=0.1)
    ```
  - <b>Drop Duplicates</b> 
    ```python
    deduped_data = dyn_frame.drop_duplicates(["column1", "column2"])
    ```
  - <b>Split Rows </b>  - Splits a DynamicFrame into multiple smaller DynamicFrames based on a condition.
    ```python
    split_data_1, split_data_2 = dyn_frame.split_rows(f=lambda row: row["column"] > 100) 
    ```
  - <b>Resolve Choice</b>  - Resolves schema ambiguities by specifying how to handle conflicting column types.
    - make_cols: creates a new column for each type
    - cast: Casts all values to specified type
    - make_struct: Creates a structure that contains each data type
    - project: Projects every type to a given type, for example project:string
    ```python
    resolved_data = dyn_frame.resolveChoice(specs=[("column", "make_struct")])
    ```
  - <b>Repartition</b> - Repartition a DynamicFrame to control the number of partitions for distributed processing
    ```python
    repartitioned_data = dyn_frame.repartition(10)
    ```
  - <b>Coalesce</b> - Coalesces the data into a smaller number of partitions.
    ```python
    coalesced_data = dyn_frame.coalesce(5)
    ```
  - <b>MapToCollection</b> - Converts a DynamicFrame to a Spark RDD collection to perform further transformations using Spark.
    ```python
    rdd_data = dyn_frame.map_to_collection()
    ```
  - <b>Pivot Transformation</b> 
    ```python
    pivoted_data = pivot_table.groupBy("column_to_group").pivot("column_to_pivot").agg({"value_column": "sum"}) 
    ```
  - <b>Aggregate </b> - Allows performing aggregation on DynamicFrame.
    ```python
    from pyspark.sql.functions import sum
    aggregated_data = dyn_frame.groupBy("group_column").agg(sum("value_column").alias("total_value"))
    ```
  - <b>Fill Missing Values</b> - Fills null or missing values with a default value.
    ```python
    filled_data = dyn_frame.fillna({"column": "default_value"})
    ```
  - <b> Merge </b> - Merges two DynamicFrames based on a key, allowing you to perform an "update" style operation.
    ```python
    merged_data = dyn_frame1.merge(dyn_frame2, keys=["id"])
    ```
  - <b> Resolve Catalog Tables </b> - If you’re working with AWS Glue Catalog tables, this allows you to directly resolve them.
    ```python
    resolved_table = glueContext.create_dynamic_frame.from_catalog(database="my_db", table_name="my_table")
    ```
  - <b>Write Dynamic Frame to S3 and MYSQL </b> - 
    ```python
    s3_write_options = {
      "path": "s3://your-bucket-name/output-path/",
      "encryption_type": "SSE-KMS",
      "kms_key": "arn:aws:kms:us-west-2:123456789012:key/your-kms-key-id"
    }

    # Write the DynamicFrame to S3 with KMS encryption
    glueContext.write_dynamic_frame.from_options(
        frame=dyn_frame,
        connection_type="s3",
        connection_options=s3_write_options,
        format="parquet"  # You can change the format to "json", "csv", etc.
    )

    #For Mysql 
    mysql_write_options = {
        "url": "jdbc:mysql://your-mysql-endpoint:3306/your-database-name",
        "dbtable": "your_table_name",
        "user": "your_username",
        "password": "your_password",
        "customJdbcDriverS3Path": "s3://path-to-your-jdbc-driver/mysql-connector-java.jar",  # Optional if you need a custom JDBC driver
        "customJdbcDriverClassName": "com.mysql.jdbc.Driver"
    }

    # Write the DynamicFrame to MySQL
    glueContext.write_dynamic_frame.from_options(
        frame=dyn_frame,
        connection_type="mysql",
        connection_options=mysql_write_options
    )
    ```
</details>

<details>
  <summary><b>Glue Workflows</b></summary>

  - AWS Glue Workflows is a feature that helps manage and automate data processing tasks in AWS Glue. It allows you to define and orchestrate a series of ETL (Extract, Transform, Load) jobs and other activities. 
  - <b> Key Features </b>
    - Visual Workflow Editor: Provides a graphical interface to design and visualize workflows, making it easier to understand the data flow and dependencies between tasks.
    - Task Dependencies: Allows you to define dependencies between different tasks, ensuring that tasks execute in the correct order based on their relationships.
    - Job Triggers: You can set up triggers to start workflows based on specific events, such as the completion of a previous job, a scheduled time, or when new data is available.
    - Monitoring and Logging: Provides monitoring capabilities to track the status of workflows and individual tasks, along with logging features to capture detailed information about job executions.
    - Error Handling: You can define actions to take when a job fails, such as retrying the job, notifying users, or executing a different task.
    - Integration with Other AWS Services: Seamlessly integrates with other AWS services, such as Amazon S3, AWS Lambda, and AWS Step Functions, allowing for more complex workflows.
    - Parameterization: You can pass parameters to jobs within a workflow, enabling dynamic and reusable workflows.
    - Version Control: Allows you to maintain different versions of workflows, which is useful for tracking changes and rolling back if necessary.
    - Cost Management: Helps manage costs by allowing users to schedule jobs during off-peak hours or only when new data is available, optimizing resource usage.
    - Data Catalog Integration: Works with the AWS Glue Data Catalog to keep track of metadata and manage schema changes in the data being processed.
</details>


<details>
  <summary><b>What is Glue Data brew?</b></summary>

  - <b> Key Features </b>
    - Visual Interface: Users can explore data visually and apply transformations using a point-and-click interface.
    - Built-in Functions: It offers a wide range of built-in functions for cleaning and transforming data, such as filtering, joining, and aggregating. 
    - Data Profiling: DataBrew provides insights into data quality, allowing users to identify anomalies and trends.
    - Integration with AWS Services: It integrates seamlessly with other AWS services, making it easier to prepare data for analytics and machine learning.
    - Job Scheduling: Users can schedule and automate data preparation tasks, ensuring that data is always ready for analysis.
    - You create <b>recipes</b> of transformations that can be saved as jobs within a larger project.
    - May define data quality rules
  - <b> Security</b>
    - Can integrate with KMS (with customer master keys only)
    - SSL in transit
    - IAM can restrict who can do what
    - CloudWatch & CloudTrail
  - PII (Personally Identifiable Information) handling
    - Enable PII statistic
    - Shuffling
    - Replace with random
    - Deterministic encryption
    - Probablistic encryption
    - Decrypt
    - masking
    - Redaction
    - Hashing

</details>
