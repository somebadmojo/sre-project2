# API Service

| Category     | SLI | SLO                                                                                                         |
|--------------|-----|-------------------------------------------------------------------------------------------------------------|
| Availability |  Successful (code != 500) http requests / total http requests   | 99%                                                                                                         |
| Latency      |  http request latency < 100ms / total http requests   | 90% of requests below 100ms                                                                                 |
| Error Budget |  Total number of errors in 7 day period   | Error budget is defined at 20%. This means that 20% of the requests can fail and still be within the budget |
| Throughput   |  API requests per second   | 5 RPS indicates the application is functioning                                                              |
