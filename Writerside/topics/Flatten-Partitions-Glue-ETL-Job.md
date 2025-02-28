# Flatten Partitions Glue ETL Job

```python
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job
from datetime import datetime, timedelta
from awsglue.dynamicframe import DynamicFrame 

sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)

job.init("glue_waf_logs_transfer", {})

today = datetime.utcnow().date()
yesterday = today - timedelta(days=1)
two_days_ago = today - timedelta(days=2)

partition_filters = [
    (f"year = '{today.year}'"
      " AND month = '{today.month:02d}'"
      " AND day = '{today.day:02d}'"),
    (f"year = '{yesterday.year}'"
      " AND month = '{yesterday.month:02d}'"
      " AND day = '{yesterday.day:02d}'"),
    (f"year = '{two_days_ago.year}'"
      " AND month = '{two_days_ago.month:02d}'"
      " AND day = '{two_days_ago.day:02d}'")
]


datasource0 = glueContext.create_dynamic_frame.from_catalog(
    database="waf-logs",
    table_name="waf_logs",
    push_down_predicate=" OR ".join(partition_filters),
    transformation_ctx="datasource0"
)

df = datasource0.toDF()

df_filtered = df.select(
    "timestamp", 
    "terminatingruleid",
    "terminatingruletype",
    "action",
    "terminatingrulematchdetails",
    "httpsourcename",
    "httpsourceid",
    "rulegrouplist",
    "ratebasedrulelist",
    "nonterminatingmatchingrules",
    "requestheadersinserted",
    "responsecodesent",
    "httprequest",
    "labels",
    "ja3fingerprint",
    "ja4fingerprint",
    "oversizefields",
    "requestbodysize",
    "requestbodysizeinspectedbywaf",
    "year",
    "month",
    "day"
)

glueContext.write_dynamic_frame.from_options(
    frame=DynamicFrame.fromDF(
        df_filtered, 
        glueContext, 
        "df_filtered"
    ),
    connection_type="s3",
    format="parquet",  # You can choose the desired output format
    connection_options={
        "path": "s3://BUCKET/AWSLogs/ACCOUNT/waf-logs-daily",
        "partitionKeys": ["year", "month", "day"]
    },
    transformation_ctx="datasink0"
)

job.commit()
```