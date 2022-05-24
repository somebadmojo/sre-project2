## Availability SLI (stat, unit percent)
### The percentage of successful requests over the last 5m
sum(rate(apiserver_request_total{job="apiserver",code!~"5.."}[5m]))/sum(rate(apiserver_request_total{job="apiserver"}[5m])) 

## Latency SLI (time series, unit ms)
### 90% of requests finish in these times
histogram_quantile(0.90,sum(rate(apiserver_request_duration_seconds_bucket{job="apiserver"}[5m])) by (le, verb))

## Throughput 
### Successful requests per second
sum(rate(apiserver_request_total{job="apiserver",code=~"2.."}[5m]))

## Error Budget - Remaining Error Budget (time series, percent)
### The error budget is 20%
# the 7 day error budget crashed my prometheus/grafana pods twice.  I reduced the time interval from 7d to 30m
1 - ((1 - (sum(increase(apiserver_request_total{job="apiserver", code="200"}[30m])) by (verb)) / sum(increase(apiserver_request_total{job="apiserver"}[30m])) by (verb)) / (1 - .80))

