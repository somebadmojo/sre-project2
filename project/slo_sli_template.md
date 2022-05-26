# API Service

| Category     | SLI | SLO                                                                                                         |
|--------------|-----|-------------------------------------------------------------------------------------------------------------|
| Availability |  Successful http requests / total http requests   | 99%                                                                            |
| Latency      |  http request latency at 0.90-quantile   | 90% of requests below 100ms                                                             |
| Error Budget |  Error Budget Remaining = 1 - (% error occurred / error budget)   | Error budget is defined at 20%. This means that 20% of the requests can fail and still be within the budget |
| Throughput   |  API requests per second   | 5 RPS indicates the application is functioning                                                        |
