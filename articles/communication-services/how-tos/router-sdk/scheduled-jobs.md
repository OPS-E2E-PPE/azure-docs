---
title: Create a scheduled job for Azure Communication Services
titleSuffix: An Azure Communication Services how-to guide
description: Use Azure Communication Services Job Router SDK to create a scheduled job
author: williamzhao
manager: bga
services: azure-communication-services

ms.author: williamzhao
ms.date: 06/07/2023
ms.topic: how-to
ms.service: azure-communication-services
ms.custom: template-how-to, devx-track-extended-java, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python

#Customer intent: As a developer, I want to create a scheduled job
---

# Scheduling a job

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

In the context of a call center, customers may want to receive a scheduled callback at a later time. As such, you will need to create a scheduled job in Job Router.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A deployed Communication Services resource. [Create a Communication Services resource](../../quickstarts/create-communication-resource.md).
- A Job Router queue with queueId `Callback` has been [created](manage-queue.md).
- A Job Router worker with channel capacity on the `Voice` channel has been [created](../../concepts/router/matching-concepts.md).
- Subscribe to the [JobWaitingForActivation event](subscribe-events.md#microsoftcommunicationrouterjobwaitingforactivation)
- Optional: Complete the quickstart to [get started with Job Router](../../quickstarts/router/get-started-router.md)

## Create a job using the ScheduleAndSuspendMode

In the following example, a job is created that will be scheduled 3 minutes from now by setting the `MatchingMode` to `ScheduleAndSuspendMode` with a `scheduleAt` parameter.  This assumes that you've already [created a queue](manage-queue.md) with the queueId `Callback` and that there is an active [worker registered](../../concepts/router/matching-concepts.md) to the queue with available capacity on the `Voice` channel.

::: zone pivot="programming-language-csharp"

```csharp
await client.CreateJobAsync(new CreateJobOptions(jobId: "job1", channelId: "Voice", queueId: "Callback")
{
    MatchingMode = new ScheduleAndSuspendMode(scheduleAt: DateTimeOffset.UtcNow.Add(TimeSpan.FromMinutes(3)))
});
```

::: zone-end

::: zone pivot="programming-language-javascript"

```typescript
await client.createJob("job1", {
    channelId: "Voice",
    queueId: "Callback",
    matchingMode: {
        modeType: "scheduleAndSuspendMode",
        scheduleAndSuspendMode: {
            scheduleAt: new Date(Date.now() + 3 * 60000)
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-python"

```python
client.create_job(job_id = "job1", router_job = RouterJob(
    channel_id = "Voice",
    queue_id = "Callback",
    matching_mode = JobMatchingMode(
        mode_type = "scheduleAndSuspendMode",
        schedule_and_suspend_mode = ScheduleAndSuspendMode(scheduled_at = datetime.utcnow() + timedelta(minutes = 3)))))
```

::: zone-end

::: zone pivot="programming-language-java"

```java
client.createJob(new CreateJobOptions("job1", "Voice", "Callback")
    .setMatchingMode(new ScheduleAndSuspendMode(OffsetDateTime.now().plusMinutes(3))));
```

::: zone-end

## Wait for the scheduled time to be reached, then queue the job

When the scheduled time has been reached, Job Router will emit a [RouterJobWaitingForActivation event](subscribe-events.md#microsoftcommunicationrouterjobwaitingforactivation).  If this event has been subscribed, the event can be parsed into a variable called `eventGridEvent`.  At this time, some required actions may be performed, before enabling the job to be matched to a worker.  For example, in the context of the contact center, such an action could be making an outbound call and waiting for the customer to accept the callback.  Once the required actions are complete, the job can be queued by calling the `UpdateJobAsync` method with the `MatchingMode` set to `QueueAndMatchMode` and priority set to `100` to quickly find an eligible worker.

::: zone pivot="programming-language-csharp"

```csharp
// Event Grid Event Handler code omitted
if (eventGridEvent.EventType == "Microsoft.Communication.RouterJobWaitingForActivation")
{
    // Perform required actions here

    await client.UpdateJobAsync(new UpdateJobOptions(jobId: eventGridEvent.Data.JobId)
    {
        MatchingMode = new QueueAndMatchMode(),
        Priority = 100
    });
}
```

::: zone-end

::: zone pivot="programming-language-javascript"

```typescript
// Event Grid Event Handler code omitted
if (eventGridEvent.EventType == "Microsoft.Communication.RouterJobWaitingForActivation")
{
    // Perform required actions here

    await client.updateJob(eventGridEvent.data.jobId, {
        matchingMode: { modeType: "queueAndMatchMode", queueAndMatchMode: {} },
        priority: 100
    });
}
```

::: zone-end

::: zone pivot="programming-language-python"

```python
# Event Grid Event Handler code omitted
if (eventGridEvent.event_type == "Microsoft.Communication.RouterJobWaitingForActivation")
{
    # Perform required actions here

    client.update_job(job_id = eventGridEvent.data.job_id,
        matching_mode = JobMatchingMode(mode_type = queueAndMatchMode, queue_and_match_mode = {}),
        priority = 100)
}
```

::: zone-end

::: zone pivot="programming-language-java"

```java
// Event Grid Event Handler code omitted
if (eventGridEvent.EventType == "Microsoft.Communication.RouterJobWaitingForActivation")
{
    // Perform required actions here

    client.updateJob(new UpdateJobOptions(eventGridEvent.Data.JobId)
        .setMatchingMode(new QueueAndMatchMode())
        .setPriority(100));
}
```

::: zone-end

## Next steps

- Learn how to [accept the Job Router offer](accept-decline-offer.md) that will be issued once a matching worker has been found for the job.
