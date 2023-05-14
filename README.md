# Squeeze

Dynamic congestion-based concurrency limits for controlling backpressure.

## What is this?

TODO: Why would someone want to use this? What problem does it solve, and how?

- How do we get maximum goodput (successful throughput) in an overloaded system?
  - Overload, goodput vs throughput
  - Need to throttle or shed load in order to protect systems from overload
- How do we prevent upstream systems from overloading downstream systems?
  - Backpressure
  - E.g. `External API -> Internal service -> Database`
  - Need to be able to _propagate_ throttling
    - Reject, drop or pause (slow down)
    - Systems need a way to communicate backpressure
- How do we detect load or overload?
  - Increases in latency
  - Load-based request failures, e.g. HTTP 429 or gRPC RESOURCE_EXHAUSTED

The Netflix one-liner:

> Java Library that implements and integrates concepts from TCP congestion control to auto-detect concurrency limits for services in order to achieve optimal throughput with optimal latency.

TODO: When shouldn't you use it?

## Limit algorithms

TODO: table comparison of the different algorithms

- Input: delay, loss or both
- Increase/decrease characteristics - additive, multiplicative or otherwise

## Background

### Rate limits vs concurrency limits

TODO:

- Rate is one dimensional
  - Doesn't consider how long things take (how expensive the work is for each request)
- Concurrency is a better measure of throughput, considering both rate and latency
  - Reacts to changes in both rate and latency, that is: more work, or more expensive work

### Static vs dynamic limits

TODO:

- Static
  - How do you know what to set it to? Even if you can work out a good number, what if that changes? E.g. a downstream system increases/decreases capacity, or the workload changes?
- Dynamic
  - Can automatically detect and respond to overload

### On congestion detection

- TCP congestion control
  - Delay-based (RTT in TCP, latency here) or loss-based (packet loss in TCP, errors caused by load here)

### Delay-based vs loss-based

- Delay-based - latency
  - TODO:
- Loss-based - request failures
  - TODO:

### Symptoms vs causes

- TODO: better title?
- Resources such as CPU, memory, threads, connections, bandwidth are the underlying bottlenecks
  - It can be hard to predict what the bottleneck will be and what effect it will have, especially in large, complex systems.
- Instead, we can measure _symptoms_ - increased latency (our own, or from other systems) or failures from overloaded systems
- In the spirit of [alerting on symptoms, not causes](https://docs.google.com/document/d/199PqyG3UsyXlwieHaqbGiWVa8eMWi8zzAn0YfcApr8Q/edit)

### Explicit vs implicit backpressure signalling

- Explicit: downstream systems sending extra data in responses about how loaded the system is which the upstream system can use as an indicator of load
  - e.g. ECN in TCP
  - Requires more coupling between services
- Implicit: upstream systems can detect increased load or certain error responses e.g. HTTP 429 or gRPC RESOURCE_EXHAUSTED

### Limiters everywhere vs at the top

- TODO: Need to think about this one
- End-to-end principle, same as for retries

## FAQ

> Does this require coordination between multiple processes?

No! The congestion detection is based on TCP congestion control algorithms which are designed to work independently. In TCP, each transmitting socket independently detects congestion and reacts accordingly.

## Prior art

- [Netflix's concurrency-limits](https://github.com/Netflix/concurrency-limits)

## Further reading

- [Wikipedia -- TCP congestion control](https://en.wikipedia.org/wiki/TCP_congestion_control)
- [AWS -- Using load shedding to avoid overload](https://aws.amazon.com/builders-library/using-load-shedding-to-avoid-overload/)
- [Sarah-Marie Nothling -- Load Series: Throttling vs Loadshedding](https://sarahnothling.wordpress.com/2019/05/12/load-series-throttling-vs-loadshedding/)
- [Myntra Engineering -- Adaptive Throttling of Indexing for Improved Query Responsiveness](https://medium.com/myntra-engineering/adaptive-throttling-of-indexing-for-improved-query-responsiveness-b3ac949e76c9)