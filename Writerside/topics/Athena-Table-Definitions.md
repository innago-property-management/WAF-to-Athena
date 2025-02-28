# Athena Table Definitions

```sql

CREATE EXTERNAL TABLE `waf_logs`(
  `timestamp` bigint, 
  `formatversion` int, 
  `webaclid` string, 
  `terminatingruleid` string, 
  `terminatingruletype` string, 
  `action` string, 
  `terminatingrulematchdetails` array<
    struct<
        conditiontype:string,
        sensitivitylevel:string,
        location:string,
        matcheddata:array<string>
    >
   >, 
  `httpsourcename` string, 
  `httpsourceid` string, 
  `rulegrouplist` array<
    struct<
        rulegroupid:string,
        terminatingrule:struct<
            ruleid:string,
            action:string,
            rulematchdetails:array<
                struct<
                    conditiontype:string,
                    sensitivitylevel:string,
                    location:string,
                    matcheddata:array<string>
                >
            >
        >,
        nonterminatingmatchingrules:array<
            struct<
                ruleid:string,
                action:string,
                overriddenaction:string,
                rulematchdetails:array<
                    struct<
                        conditiontype:string,
                        sensitivitylevel:string,
                        location:string,
                        matcheddata:array<string>
                    >
                >,
                challengeresponse:struct<
                    responsecode:string,
                    solvetimestamp:string
                >,
                captcharesponse:struct<
                    responsecode:string,
                    solvetimestamp:string
                >
            >
        >,
        excludedrules:string
    >
   >, 
  `ratebasedrulelist` array<
    struct<
        ratebasedruleid:string,
        limitkey:string,
        maxrateallowed:int
    >
   >, 
  `nonterminatingmatchingrules` array<
    struct<
        ruleid:string,
        action:string,
        rulematchdetails:array<
            struct<
                conditiontype:string,
                sensitivitylevel:string,
                location:string,
                matcheddata:array<string>
            >
        >,
        challengeresponse:struct<
            responsecode:string,
            solvetimestamp:string
        >,
        captcharesponse:struct<
            responsecode:string,
            solvetimestamp:string
        >
    >
   >, 
  `requestheadersinserted` array<struct<name:string,value:string>>, 
  `responsecodesent` string, 
  `httprequest` struct<
    clientip:string,
    country:string,
    headers:array<
        struct<
            name:string,
            value:string
        >
    >,
    uri:string,
    args:string,
    httpversion:string,
    httpmethod:string,
    requestid:string
   >, 
  `labels` array<struct<name:string>>, 
  `captcharesponse` struct<
    responsecode:string,
    solvetimestamp:string,
    failurereason:string
   >, 
  `challengeresponse` struct<
    responsecode:string,
    solvetimestamp:string,
    ßfailurereason:string
   >, 
  `ja3fingerprint` string, 
  `ja4fingerprint` string, 
  `oversizefields` string, 
  `requestbodysize` int, 
  `requestbodysizeinspectedbywaf` int)
PARTITIONED BY ( 
  `year` int COMMENT '', 
  `month` int COMMENT '', 
  `day` int COMMENT '', 
  `hour` int, 
  `minute` int)
ROW FORMAT SERDE 
  'org.openx.data.jsonserde.JsonSerDe' 
STORED AS INPUTFORMAT 
  'org.apache.hadoop.mapred.TextInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION
  's3://BUCKET_NAME/AWSLogs/ACCOUNT_ID/WAFLogs/REGION/WAF_ACL_NAME'
TBLPROPERTIES (
  'compressionType'='gzip', 
  'projection.day.format'='dd', 
  'projection.day.interval'='1', 
  'projection.day.range'='1,31', 
  'projection.day.type'='integer', 
  'projection.day.unit'='DAYS', 
  'projection.enabled'='true', 
  'projection.hour.range'='0,24', 
  'projection.hour.type'='integer', 
  'projection.hour.units'='HOURS', 
  'projection.minute.range'='0,60', 
  'projection.minute.type'='integer', 
  'projection.minute.units'='MINUTES', 
  'projection.month.format'='MM', 
  'projection.month.range'='1,12', 
  'projection.month.type'='integer', 
  'projection.month.unit'='MONTHS', 
  'projection.year.format'='yyyy', 
  'projection.year.range'='2025,2999', 
  'projection.year.type'='integer', 
  'projection.year.unit'='YEARS', 
  'storage.location.template'=
    's3://BUCKET_NAME/AWSLogs/ACCOUNT_ID/WAFLogs/REGION/WAF_ACL_NAME/'
        + '${year}/${month}/${day}/${hour}/${minute}/')

```

Replace:
- BUCKET_NAME
- ACCOUNT_ID
- REGION
- WAF_ACL_NAME

The templating is important. It must match the structure of the logs the WAF is creating in the s3 bucket. 

`'s3://BUCKET_NAME/AWSLogs/ACCOUNT_ID/WAFLogs/REGION/WAF_ACL_NAME/${year}/${month}/${day}/${hour}/${minute}/'`

```sql
CREATE EXTERNAL TABLE `waf_logs_daily`(
  `timestamp` bigint, 
  `terminatingruleid` string, 
  `terminatingruletype` string, 
  `action` string, 
  `httpsourcename` string, 
  `httpsourceid` string, 
  `rulegrouplist` array<
    struct<
        rulegroupid:string,
        terminatingrule:struct<
            ruleid:string,
            action:string,
            rulematchdetails:array<
                struct<
                    conditiontype:string,
                    sensitivitylevel:string,
                    location:string,
                    matcheddata:array<string>
                >
            >
        >,
        nonterminatingmatchingrules:array<
            struct<
                ruleid:string,
                action:string,
                overriddenaction:string,
                rulematchdetails:array<
                    struct<
                        conditiontype:string,
                        sensitivitylevel:string,
                        location:string,
                        matcheddata:array<string>
                    >
                >,
                challengeresponse:struct<
                    responsecode:string,
                    solvetimestamp:string
                >,
                captcharesponse:struct<r
                    esponsecode:string,
                    solvetimestamp:string>
                >
            >,
            excludedrules:string
        >
    >, 
  `ratebasedrulelist` array<
        struct<
            ratebasedruleid:string,
            limitkey:string,
            maxrateallowed:int
        >
    >, 
  `nonterminatingmatchingrules` array<
        struct<
            ruleid:string,
            action:string,
            rulematchdetails:array<
                struct<
                    conditiontype:string,
                    sensitivitylevel:string,
                    location:string,
                    matcheddata:array<string>
                >
            >,
            challengeresponse:struct<
                responsecode:string,
                solvetimestamp:string
            >,
            captcharesponse:struct<
                responsecode:string,
                solvetimestamp:string
            >
        >
    >, 
  `requestheadersinserted` array<
        struct<
            name:string,
            value:string
        >
    >, 
  `responsecodesent` string, 
  `httprequest` struct<
    clientip:string,
    country:string,
    headers:array<
        struct<
            name:string,
            value:string
        >
    >,
    uri:string,
    args:string,
    httpversion:string,
    httpmethod:string,
    requestid:string
   >, 
  `labels` array<struct<name:string>>, 
  `ja3fingerprint` string, 
  `ja4fingerprint` string, 
  `oversizefields` string)
PARTITIONED BY ( 
  `year` int COMMENT '', 
  `month` int COMMENT '', 
  `day` int COMMENT '')
ROW FORMAT SERDE 
  'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe' 
STORED AS INPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION
  's3://BUCKET_NAME/AWSLogs/ACCOUNT_ID/waf-logs-daily'
```