
### High-level architecture

```text
Public users                              Internal users
Learners, community, volunteers           Staff, executives, board
        |                                            |
        v                                            v
+-------------------------+              +-------------------------+
| Next.js public portal   |              | Slack bot / AI assistant|
| Hosted on Vercel        |              | Bolt for JavaScript     |
+-----------+-------------+              +------------+------------+
            |                                         |
            +----------------+------------------------+
                             v
               +------------------------------+
               | Node.js server-side layer     |
               | Next.js API routes or service |
               | Tool gateway + authorization  |
               +-----------+--------------+----+
                           |              |
                           v              v
              +------------------+  +----------------------+
              | Salesforce       |  | Supabase             |
              | Standard objects |  | Postgres, mock data, |
              | Connected App    |  | logs, approvals, RLS |
              +------------------+  +----------------------+
```
### Bot purpose

The Slack bot is an **internal, decision-support agent**. It does not replace program staff or make decisions by itself.

It should:

- Provide concise, source-aware summaries.
- Distinguish facts from risk interpretation and recommendations.
- Surface data freshness.
- Request explicit human approval before generating a Salesforce Task or updating an approval state.
- Keep public portal data separate from internal data.

### MVP Slack commands

| Command | Purpose | Example output |
|---|---|---|
| `/board-brief` | Produce a board-ready draft | Fundraising, learner outcomes, risks, decisions needed |
| `/project-risk` | List prioritized program/project risks | Completion gap, mentor capacity gap, overdue action |
| `/escalate-risk` | Start a governed escalation workflow | Approval card to create Salesforce Task |
| `/decision-queue` | Show pending approvals | Board brief and risk escalation approvals |
| `/learner-progress` | Optional aggregate learner progress summary | Active learners, stalled learners, completion trend |

### Natural-language examples

```text
@ImpactPulse What is at risk this week?

@ImpactPulse Create a board brief for the current reporting period.

@ImpactPulse Why is Salesforce Career Foundations at risk?

@ImpactPulse What should the executive director do next?

@ImpactPulse Show the evidence behind this risk.

### Response format

The agent should use this consistent structure:

```text
Current status

Key facts
- Fact with source and time context

Risk or exception
- Explanation of why it needs attention

Recommended next action
- A clearly labeled suggestion, not an autonomous decision

Available actions
[Open dashboard] [Review evidence] [Request approval]

Source: Salesforce + ImpactPulse demo data | Updated: [timestamp]
```
### Approval flows

#### Risk escalation approval

```text
ImpactPulse approval request

Action: Escalate the Salesforce Career Foundations completion-risk to the Executive Director.

This will:
1. Create a high-priority Salesforce Task assigned to the Executive Director.
2. Record the escalation in the ImpactPulse audit log.
3. Mark the risk as escalated in Supabase.

Evidence:
- Completion rate: 42% vs. 65% target
- 18 learners stalled in the last 14 days
- 2 mentors available for 18 learners needing support

[Approve escalation] [Reject]
```

#### Board brief approval

```text
ImpactPulse approval request

Action: Approve this board brief for internal review.

This will:
1. Mark the board brief as approved in ImpactPulse.
2. Create a Salesforce Task for the executive/board liaison to distribute or review it.
3. Record the approval in the audit log.

This will not send an external email or publish data automatically.

[Approve board brief] [Reject]
```

#### Board brief approval

```text
ImpactPulse approval request

Action: Approve this board brief for internal review.

This will:
1. Mark the board brief as approved in ImpactPulse.
2. Create a Salesforce Task for the executive/board liaison to distribute or review it.
3. Record the approval in the audit log.

This will not send an external email or publish data automatically.

[Approve board brief] [Reject]
```


