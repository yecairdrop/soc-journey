# SPL Quick Reference
```
index=<idx> sourcetype=<st> <terms>      base search: most selective terms first, set time range
| stats count, dc(x), values(x) by y     aggregate
| eval new=if(cond,a,b)                  compute
| where count > N                        filter after stats
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"  extract field with regex
| bin _time span=5m                      time buckets (threshold alerts)
| timechart span=1h count by field       series over time
| table a b c | sort -count | head 20    shape the output
| dedup user | top limit=10 field | rare field
| rename a as b | fillnull value="-"
| transaction src_ip maxspan=10m         group related events
```
Detection shape: `filter → bin _time → stats count by X → where count >= N`.
