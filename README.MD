### Chapter 1: Introduction
Two approaches to hiring people to manage system stability:

#### Traditional approach: sysadmins

- Assemble existing components and deploy to produce a service
- Respond to events and updates as they occur
- Grow team to absorb increased work as service grows
- Pros
  - Easy to implement because it’s standard
  - Large talent pool to hire from
  - Lots of available software
- Cons
  - Manual intervention for change management and event handling causes size of team to scale with load on system
  - Ops is fundamentally at odds with dev, which can cause pathological resistance to changes, which causes similarly pathological response from devs, which reclassify “launches” as “incremental updates”, “flag flips”, etc.

#### Google’s approach: SREs

- Have software engineers do operations
- Candidates should be able to pass or nearly pass normal dev hiring bar, and may have some additional skills that are rare among devs (e.g., L1 - L3 networking or UNIX system internals).
- Career progress comparable to dev career track
- Results
  - SREs would be bored by doing tasks by hand
  - Have the skillset necessary to automate tasks
  - Do the same work as an operations team, but with automation instead of manual labor
- To avoid manual labor trap that causes team size to scale with service load, Google places a 50% cap on the amount of “ops” work for SREs
  - Upper bound. Actual amount of ops work is expected to be much lower
- Pros
  - Cheaper to scale
  - Circumvents devs/ops split
- Cons
  - Hard to hire for
  - May be unorthodox in ways that require management support (e.g., product team may push back against decision to stop releases for the quarter because the error budget is depleted)

#### Tenets of SRE

- SRE team responsible for **latency, performance, efficiency, change management, monitoring, emergency response, and capacity planning**

#### Ensuring a durable focus on engineering

- 50% ops cap means that extra ops work is redirected to product teams on overflow
- Provides feedback mechanism to product teams as well as keeps load down
- Target max 2 events per 8-12 hour on-call shift
- Postmortems for all serious incidents, even if they didn’t trigger a page
- Blameless postmortems

#### Move fast without breaking SLO

- Error budget. 100% is the wrong reliability target for basically everything
- Going from 5 9s to 100% reliability isn’t noticeable to most users and requires tremendous effort
- Set a goal that acknowledges the trade-off and leaves an error budget
- Error budget can be spent on anything: launching features, etc.
- Error budget allows for discussion about how phased rollouts and 1% experiments can maintain tolerable levels of errors
- Goal of SRE team isn’t “zero outages” -- SRE and product devs are incentive aligned to spend the error budget to get maximum feature velocity

#### Monitoring

- Monitoring should never require a human to interpret any part of the alerting domain
- Three valid kinds of monitoring output
  - Alerts: human needs to take action immediately
  - Tickets: human needs to take action eventually
  - Logging: no action needed
  - Note that, for example, graphs are a type of log

#### Emergency Response

- Reliability is a function of MTTF (mean-time-to-failure) and MTTR (mean-time-to-recovery)
- For evaluating responses, we care about MTTR
- Humans add latency
- Systems that don’t require humans to respond will have higher availability due to lower MTTR
- Having a “playbook” produces 3x lower MTTR
  - Having hero generalists who can respond to everything works, but having playbooks works better

#### Change management

- 70% of outages due to changes in a live system. Mitigation:
  - Implement progressive rollouts
  - Monitoring
  - Rollback
- Remove humans from the loop, avoid standard human problems on repetitive tasks

#### Demand forecasting and [capacity planning](http://www.amazon.com/gp/product/0596518579/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0596518579&linkCode=as2&tag=abroaview-20&linkId=EK2PBWYSHYK26GIV)

- Straightforward, but a surprising number of teams/services don’t do it

#### Provisioning

- Adding capacity riskier than load shifting, since it often involves spinning up new instances/locations, making significant changes to existing systems (config files, load balancers, etc.)
- Expensive enough that it should be done only when necessary; must be done quickly
  - If you don’t know what you actually need and overprovision that costs money

#### Efficiency and performance

- Load slows down systems
- SREs provision to meet capacity target with a specific response time goal
- Efficiency == money

### Chapter 2: The production environment at Google, from the viewpoint of an SRE

### Chapter 3: Embracing risk

### Chapter 4: Service level objectives
- Ex: Chubby planned outages
  - Google found that Chubby was consistently over its SLO, and that global Chubby outages would cause unusually bad outages at Google
  - Chubby was so reliable that teams were incorrectly assuming that it would never be down and failing to design systems that account for failures in Chubby
  - Solution: take Chubby down globally when it’s too far above its SLO for a quarter to “show” teams that Chubby can go down

#### What do you and your users care about?

- Too many indicators: hard to pay attention
- Too few indicators: might ignore important behavior
- Different classes of services should have different indicators
  - User-facing: availability, latency, throughput
  - Storage: latency, availability, durability
  - Big data: throughput, end-to-end latency
- All systems care about correctness

#### Collecting indicators

- Can often do naturally from server, but client-side metrics sometimes needed.

#### Aggregation

- Use distributions and not averages
- User studies show that people usually prefer slower average with better tail latency
- Standardize on common defs, e.g., average over 1 minute, average over tasks in cluster, etc.
  - Can have exceptions, but having reasonable defaults makes things easier

#### Choosing targets

- Don’t pick target based on current performance
  - Current performance may require heroic effort
- Keep it simple
- Avoid absolutes
  - Unreasonable to talk about “infinite” scale or “always” available
- Minimize number of SLOs
- Perfection can wait
  - Can always redefine SLOs over time
- SLOs set expectations
  - Keep a safety margin (internal SLOs can be defined more loosely than external SLOs)
- Don’t overachieve
  - See Chubby example, above
  - Another example is making sure that the system isn’t too fast under light loads

### Chapter 5: Eliminating toil

### Chapter 6: Monitoring distributed systems

- Why monitor?
  - Analyze long-term trends
  - Compare over time or do experiments
  - Alerting
  - Building dashboards
  - Debugging

#### Setting reasonable expectations

- Monitoring is non-trivial
- 10-12 person SRE team typically has 1-2 people building and maintaining monitoring
- Number has decreased over time due to improvements in tooling/libs/centralized monitoring infra
- General trend towards simpler/faster monitoring systems, with better tools for post hoc analysis
- Avoid “magic” systems
- Limited success with complex dependency hierarchies (e.g., “if DB slow, alert for DB, otherwise alert for website”).
  - Used mostly (only?) for very stable parts of system
- Rules that generate alerts for humans should be simple to understand and represent a clear failure

- Lots of white-box monitoring
- Some black-box monitoring for critical stuff
- Four golden signals
  - Latency
  - Traffic
  - Errors
  - Saturation

#### The long run

- There’s often a tension between long-run and short-run availability
- Can sometimes fix unreliable systems through heroic effort, but that’s a burnout risk and also a failure risk
- Taking a controlled hit in short-term reliability is usually the better trade

### Chapter 7: Evolution of automation at Google

### Chapter 8: Release engineering

### Chapter 9: Simplicity

### Chapter 10: Altering

#### Borgmon

- Similar-ish to [Prometheus](https://prometheus.io/)
- Common data format for logging
- Data used for both dashboards and alerts
- Formalized a legacy data format, “varz”, which allowed metrics to be viewed via HTTP
  - To view metrics manually, go to [http://foo:80/varz](http://foo:80/varz)
- Adding a metric only requires a single declaration in code
  - low user-cost to add new metric
- Borgmon fetches /varz from each target periodically
  - Also includes synthetic data like health check, if name was resolved, etc.,
- Time series arena
  - Data stored in-memory, with checkpointing to disk
  - Fixed sized allocation
  - GC expires oldest entries when full
  - conceptually a 2-d array with time on one axis and items on the other axis
  - 24 bytes for a data point -> 1M unique time series for 12 hours at 1-minute intervals = 17 GB
- Borgmon rules
  - Algebraic expressions
  - Compute time-series from other time-series
  - Rules evaluated in parallel on a threadpool
- Counters vs. gauges
  - Def: counters are non-decreasing
  - Def: can take any value
  - Counters preferred to gauges because gauges can lose information depending on sampling interval
- Altering
  - Borgmon rules can trigger alerts
  - Have minimum duration to prevent “flapping”
  - Usually set to two duration cycles so that missed collections don’t trigger an alert
- Scaling
  - Borgmon can take time-series data from other Borgmon (uses binary streaming protocol instead of the text-based varz protocol)
  - Can have multiple tiers of filters
- Prober
  - Black-box monitoring that monitors what the user sees
  - Can be queried with varz or directly send alerts to Altertmanager
- Configuration
  - Separation between definition of rules and targets being monitored

### Chapter 11: Being on-call

### Chapter 12: Effective troubleshooting

### Chapter 13: Emergency response

### Chapter 14: Managing incidentsis an area where we seem to actually be pretty good. No notes on this chapter._

### Chapter 15: Postmortem culture: learning from failure

### Chapter 16: Tracking outages

### Chapter 17: Testing for reliability

### Chapter 18: Software engineering in SRE

### Chapter 19: Load balancing at the frontend

### Chapter 20: Load balancing in the datacenter

### Chapter 21: Handling overload

### Chapter 22: Addressing cascading failures

### Chapter 23: Distributed consensus for reliability

### Chapter 24: Distributed cron

### Chapter 25: Data processing pipelines

### Chapter 26: Data integrity

### Chapter 27: Reliable product launches at scale
