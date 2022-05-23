## Availability SLI
### The percentage of successful requests over the last 5m

http_request_total { code =~ "200" } / http_request_total
http_request_total{code =~ "200"}

## Latency SLI
### 90% of requests finish in these times

histogram_quantile (
  0.90,
  sum by (le, verb, instance)(
    rate(apiserver_request_latencies_bucket[100ms])
  )
) / 1e3

## Throughput
### Successful requests per second

sum(rate(http_request_total{code=~"2.."}[5m]))

## Error Budget - Remaining Error Budget
### The error budget is 20%

1 - ((1 - (sum(increase(http_request_total{job="webserver", code="200"}[7d])) by (verb)) /  sum(increase(http_request_total{job="webserver"}[7d])) by (verb)) / (1 - .90))