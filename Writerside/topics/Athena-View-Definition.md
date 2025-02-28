# Athena View Definition

```sql
CREATE OR REPLACE VIEW "jwt_view" AS 
SELECT
  timestamp
, action
, ja4fingerprint
, ja3fingerprint
, httprequest.clientIp
, httprequest.country
, httprequest.uri
, element_at(
    transform(
        filter(
            httprequest.headers, (x) -> REGEXP_LIKE(x.name, '[Hh]ost')
        ), 
        (x) -> x.value
    ), 
    1
  ) host
, TRY(
    from_utf8(
        from_base64(
            split_part(
                element_at(
                    transform(
                        filter(
                            httprequest.headers, 
                            (x) -> REGEXP_LIKE(x.name, 
                                '[Aa]uthorization')
                        ), 
                        (x) -> x.value
                    ), 
                    1
                ),
                '.', 
                2
            )
        )
    )
  ) token
, TRY(
    json_extract_scalar(
        from_utf8(
            from_base64(
                split_part(
                    element_at(
                        transform(
                            filter(
                                httprequest.headers, 
                                (x) -> REGEXP_LIKE(x.name, 
                                    '[Aa]uthorization')
                            ), 
                            (x) -> x.value
                        ), 
                        1
                    ),
                    '.',
                    2
                )
            )
        ), 
        '$.email'
    )
  ) email
FROM
  waf_logs_daily
WHERE (
    cardinality(
        transform(
            filter(
                httprequest.headers, 
                (x) -> REGEXP_LIKE(x.name, '[Aa]uthorization')
            ), 
            (x) -> x.name
        )
    ) > 0
)


```