---
title: 'Из книги "Web Operations" - Postmortem (RCA)'
tags: [book]
---
Продолжаю постить выдержки из книги "Web Operations: Keeping the Data on Time".

В этом посте речь пойдёт о проведении Postmortem, или Root-Cause Analysis (RCA). По сути, это - анализ произошедшей аварии. В книге действительно хорошо всё описано, поэтому выдержки приведу as is.

### What Is a Postmortem?

A postmortem needs to cover these essentials at a minimum:
1. A description of the incident
2. A description of the root cause
3. How the incident was stabilized and/or fixed
4. A timeline of actions taken to resolve the incident
5. How the incident affected customers
6. Remediations or corrective actions

The first five items make sure everyone involved has a common understanding of the facts. Many incidents reoccur because people do not understand what really happened and how the problem was fixed. Different teams and different layers of management arrive at the postmortem with different understandings of what happened. During a postmortem, everyone with significant involvement in the incident should be present at the same time to document a common description of the facts of the incident. Without an accurate account of the facts, it will be impossible to determine and prioritize the corrective actions that are the biggest benefit of a postmortem.

Determining the root cause should go without saying, but I can’t tell you the number of times I have been in a postmortem where participants spent tons of time debating each possible remediation item or the number of customers affected, only to find that they had wasted their time because they didn’t have the root cause right.

The same goes for the stabilizing steps. Often during the chaos of a major incident, multiple people attempt multiple fixes. Determine the true root cause and the step that brought it to stable before moving on. Note that an incident might be stable without actually being fixed. You can eliminate customer impact without fixing an issue like when you reboot servers to address a memory leak. Although it will be stable for a short period, the servers are just going to run out of memory again if the root cause is not addressed.

A timeline will be important for determining how the incident could have been fixed more quickly. Again, multiple people may have a different understanding of the time- line. Allow each participant to contribute the items they are aware of before moving to remediation items around decreasing Time to Resolve (TTR). Make sure to answer the following questions:

- When did the incident begin to affect customers? (Note: Not all incidents affect customers.)
- When did someone in the organization first become aware that there was a problem?
- How did this person become aware? Through monitoring? The Customer Care team? A personal report?
- How long did it take for the knowledge of the incident to get to the person who ultimately resolved it?
- What would have allowed someone to diagnose the fault earlier? (For example, better monitoring, more comprehensive troubleshooting guides, etc.)
- Did the stabilizing steps take a long time to implement? Could they be automated or simplified to speed them up?

Reducing the TTR of an incident is every bit as important as eliminating incidents themselves. Ultimately, total customer impact minutes (TTR × Number of Customers Affected) is what counts. Some outages may be unavoidable, but if you can ensure quick recovery, your customers will benefit.

After determining the customer impact, you may want to assign a severity level to the incident. You can develop your own severity categories, or use this example:

Severity 1: site outages affecting a significant number of customers

Severity 2: site degradation, performance issues, or broken features which are difficult to work around

Severity 3: other service issues that have minimal customer impact or easy workarounds

Assigning a severity level will help you to prioritize completion of your remediation items and is also useful during the triage stage of an active incident. You may have already assigned a severity level while attempting to resolve the issue so that you could determine whether it is a five-alarm fire requiring all hands on deck or a minor blip.

### When to Conduct a Postmortem.

You should conduct a postmortem after every major outage that affects customers, preferably within 24 hours. This is a little more difficult than it sounds. Teams are usually busy. They are especially busy right after an incident occurs, because they probably spent unplanned cycles on firefighting. Some of the firefighters may have been up all night resolving the incident. Once an incident is stable, people have a tendency to get back to whatever they were doing before they were interrupted to try to make up for lost time.

The important thing to note is that until a postmortem is conducted and corrective actions are identified, your site is at risk of repeating the incident. If you can’t conduct the postmortem within 24 hours, don’t wait any longer than a week. After a week, incident participants will start to forget key details; you may be missing key logfiles; and of course, you remain at risk for reoccurrence.

Although it’s good to complete a postmortem within 24 hours, you should not conduct a postmortem while the incident is still open. Trying to determine preventive actions or assign blame is a distraction that teams don’t need while they are attempting to stabi- lize the service. Remember, this process is ultimately intended to benefit your customers, and the process should never directly get in the way of restoring service to them.

### Postmortem Follow-Up
...

After many years of postmortems, I’ve found a few areas that you will likely want to consider for corrective actions. I call this _site operability_.

_Eliminate single points of failure_

Hardware can and will fail. Utilize redundancy to protect yourself. Don’t let hardware failure be a cause of a customer-impacting incident.

_Capacity planning_

Understand your site and its future capacity needs. Base your capacity planning on total utilization of primary constraints such as CPU, memory, I/O, and storage, not on secondary constraints such as number of users. Provision in the areas you need, before you need them.

_Monitoring_

This is essential for detecting and diagnosing incidents. Other chapters in this book provide great recommendations for monitoring techniques.

_Release management_

Change is historically the most likely cause of incidents. Make sure your release process has the right quality controls in place. Consider implementing concepts such as automated testing, staging environments, limited production deployments, dark launches (rolling out code without activating functionality for customers until the code is proven stable), and the ability to roll back immediately.

_Operational architecture reviews_

Hold an architecture review that is entirely focused on how the new release or product will perform in the production environment before rolling it out to customers. Consider maintainability, failure scenarios, and incident response as well as architectural reliability and scalability.

_Configuration management_

As your systems grow, production configurations become increasingly ­complicated. The inability to understand the implications of changes to production configurations often leads to incidents attributed to human error. Having a configuration management system that is straightforward and logical will help engineers avoid unintended issues. Read Chapter 5 in this book for more recommendations.

_On-call and escalation process_

Identify the issue and get it to the person who can resolve it as quickly as possible.

_Unstable components_

Identify and fix software components with a history of crashing and unintended behavior. Make it a priority even when there is an easy manual fix. Those manual fixes add up to become a drag on customer experience and your ability to scale and be effective.

By proactively ensuring that your site is operable, you will be able to avoid many painful postmortems before they are even necessary.
