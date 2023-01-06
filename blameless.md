# Blameless Post Mortem

## What?

> Learn from the mistakes of others. You can't live long enough to make them all yourself. - Eleanor Roosevelt 

A blameless post mortem is just a summation of what the words mean!

* Blameless - Without Fault
* Post - After
* Mortem - Death, Finished

Okay, maybe not exactly what the words mean.
Breaking it down - it is simply an activity occurring after our systems have been affected in a negative way.
It is an event that attempts to analyze root cause without assigning any fault.
All observations are meticulously and purposefully phrased in a constructive way. 

| Traditional | Blameless | 
| ----------- | --------- |
| The outage was caused when Josh dropped tables in production, unbelievable.	 | 10 tables were dropped in production by a user with over-elevated privileges. |
| The `${FICTIONAL_TEAM}`  didn't let us know they changed their `${FICTIONAL_API}` , preventing us from using them successfully.	| The `${FICTIONAL_API}` is not versioned. We do not have a good way to depreciate old functionality or change existing functionality. |
| Ken broke CI for the enterprise by inappropriately scoping an automated test.	| Bamboo couldn't gracefully shut down when too much load was added, causing the freeze. |

## Why?

There are three reasons to run a blameless post mortem:

1. find the true root cause(s) of an issue
2. understand how we determined the cause of the problem
3. create action items that prevent the issue from recurring

First - finding the root cause(s) of an issue can be difficult, especially when people are incentivized not to share.
As individuals, groups, or departments attempt to avoid blame, the circumstances that led to our systems being negatively affected becomes opaque.
We are less likely to share things that portray the in-group in an unflattering light.
Transparency is reduced at a rate directly proportional to how important it is to avoid blame.

Second - understanding how we determined cause.
Disseminating knowledge about what Grafana charts we looked at, and why we hypothesized we should go with that specific chart, in order to diagnose the issue.
Sharing knowledge about debugging the system helps everyone involved to fill in gaps of knowledge.

Third - stopping it from happening again. It doesn't matter *who* caused it but *how* it was caused.
We can use how it was caused to highlight preventative measures that can be taken to avoid recurrence.
For our previous – and definitely, 100% fictional – example, is it important that Josh the Junior Dev dropped tables in production?
Yes, but it is more important to know we are not adequately assigning permissions that prevent a drop.
~~Why on Earth would I~~ Josh the Junior Dev in this **extremely fictional example** would be unlikely to come forward with what happened if he feared retribution.
Anyone could've run a script with elevated privileges they shouldn't have had - even the application itself with a database management tool like Flyway. At the end of the day, the who wasn't the important thing to fix.

## How?

There are a lot of great systems for running a blameless retrospective - but the easiest is simply to dissect the root cause of a problem without ever asking any question that attempts to identify the *who*.

Additional Reading:

[Atlassian Documentation](https://www.atlassian.com/incident-management/postmortem/blameless)
[Google Documentation](https://sre.google/sre-book/postmortem-culture/)

