# Glue Crawler to move logs into table

*The following has been edited to show just the values that need to be set.*

```json
{
    "Name": "waf logs to table waf_logs",
    "Role": "service-role/AWSGlueServiceRole-waf-logs",
    "Targets": {
        "CatalogTargets": [
            {
                "DatabaseName": "waf-logs",
                "Tables": [
                    "waf_logs"
                ]
            }
        ]
    },
    "DatabaseName": "waf-logs",
    "Description": "logs to table",
    "Classifiers": [
        "generic-json"
    ],
    "RecrawlPolicy": {
        "RecrawlBehavior": "CRAWL_EVERYTHING"
    },
    "SchemaChangePolicy": {
        "UpdateBehavior": "LOG",
        "DeleteBehavior": "LOG"
    },
    "LineageConfiguration": {
        "CrawlerLineageSettings": "DISABLE"
    },
    "TablePrefix": "waf-",
    "Schedule": {
        "ScheduleExpression": "cron(00 03 * * ? *)",
        "State": "SCHEDULED"
    },
    "Configuration": {
      "Grouping": {
        "TableGroupingPolicy": "CombineCompatibleSchemas"
      }
    }
}
```