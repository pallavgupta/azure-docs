---	
title: Azure Function Rule concepts for Azure Communication Services
titleSuffix: An Azure Communication Services how-to guide
description: Learn how to customize how workers are ranked for the best worker mode
author: rsarkar
manager: bo.gao
services: azure-communication-services

ms.author: rsarkar
ms.date: 02/23/2022
ms.topic: how-to
ms.service: azure-communication-services
---	

# How to customize how workers are ranked for the best worker distribution mode

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

The `best-worker` distribution mode selects the workers that are best able to handle the job first. The logic to rank Workers can be customized, with an expression or Azure function to compare two workers. The following example shows how to customize this logic with your own Azure Function.

## Scenario: Custom scoring rule in best worker distribution mode

We want to distribute offers among their workers associated with a queue. The workers will be given a score based on their labels and skill set. The worker with the highest score should get the first offer (_BestWorker Distribution Mode_).

:::image type="content" source="./media/best-worker-distribution-mode-problem-statement.png" alt-text="Diagram showing Best Worker Distribution Mode problem statement" lightbox="./media/best-worker-distribution-mode-problem-statement.png":::

### Situation

- A job has been created and classified.
  - Job has the following **labels** associated with it
    - ["CommunicationType"] = "Chat"
    - ["IssueType"] = "XboxSupport"
    - ["Language"] = "en"
    - ["HighPriority"] = true
    - ["SubIssueType"] = "ConsoleMalfunction"
    - ["ConsoleType"] = "XBOX_SERIES_X"
    - ["Model"] = "XBOX_SERIES_X_1TB"
  - Job has the following **WorkerSelectors** associated with it
    - ["English"] >= 7
    - ["ChatSupport"] = true
    - ["XboxSupport"] = true
- Job currently is in a state of '**Queued**'; enqueued in *Xbox Hardware Support Queue* waiting to be matched to a worker.
- Multiple workers become available simultaneously.
  - **Worker 1** has been created with the following **labels**
    - ["HighPrioritySupport"] = true
    - ["HardwareSupport"] = true
    - ["Support_XBOX_SERIES_X"] = true
    - ["English"] = 10
    - ["ChatSupport"] = true
    - ["XboxSupport"] = true
  - **Worker 2** has been created with the following **labels**
    - ["HighPrioritySupport"] = true
    - ["HardwareSupport"] = true
    - ["Support_XBOX_SERIES_X"] = true
    - ["Support_XBOX_SERIES_S"] = true
    - ["English"] = 8
    - ["ChatSupport"] = true
    - ["XboxSupport"] = true
  - **Worker 3** has been created with the following **labels**
    - ["HighPrioritySupport"] = false
    - ["HardwareSupport"] = true
    - ["Support_XBOX"] = true
    - ["English"] = 7
    - ["ChatSupport"] = true
    - ["XboxSupport"] = true

### Expectation

We would like the following behavior when scoring workers to select which worker gets the first offer.

:::image type="content" source="./media/best-worker-distribution-mode-scoring-rule.png" alt-text="Decision flow diagram for scoring worker" lightbox="./media/best-worker-distribution-mode-scoring-rule.png":::

The decision flow (as shown above) is as follows:

- If a job is **NOT HighPriority**:
  - Workers with label: **["Support_XBOX"] = true**; get a score of *100*
  - Otherwise, get a score of *1*

- If a job is **HighPriority**:
  - Workers with label: **["HighPrioritySupport"] = false**; get a score of *1*
  - Otherwise, if **["HighPrioritySupport"] = true**:
    - Does Worker specialize in console type -> Does worker have label: **["Support_<**jobLabels.ConsoleType**>"] = true**? If true, worker gets score of *200*
    - Otherwise, get a score of *100*

## Creating an Azure function

Before moving on any further in the process, let us first define an Azure function that scores worker.
> [!NOTE]
> The following Azure function is using JavaScript. For more information, please refer to [Quickstart: Create a JavaScript function in Azure using Visual Studio Code](../../../azure-functions/create-first-function-vs-code-node.md)

Sample input for **Worker 1**

```json
{
  "job": {
    "CommunicationType": "Chat",
    "IssueType": "XboxSupport",
    "Language": "en",
    "HighPriority": true,
    "SubIssueType": "ConsoleMalfunction",
    "ConsoleType": "XBOX_SERIES_X",
    "Model": "XBOX_SERIES_X_1TB"
  },
  "selectors": [
    {
      "key": "English",
      "operator": "GreaterThanEqual",
      "value": 7,
      "ttl": null
    },
    {
      "key": "ChatSupport",
      "operator": "Equal",
      "value": true,
      "ttl": null
    },
    {
      "key": "XboxSupport",
      "operator": "Equal",
      "value": true,
      "ttl": null
    }
  ],
  "worker": {
    "Id": "e3a3f2f9-3582-4bfe-9c5a-aa57831a0f88",
    "HighPrioritySupport": true,
    "HardwareSupport": true,
    "Support_XBOX_SERIES_X": true,
    "English": 10,
    "ChatSupport": true,
    "XboxSupport": true
  }
}
```

Sample implementation:

```javascript
module.exports = async function (context, req) {
    context.log('Best Worker Distribution Mode using Azure Function');

    let score = 0;
    const jobLabels = req.body.job;
    const workerLabels = req.body.worker;

    const isHighPriority = !!jobLabels["HighPriority"];
    context.log('Job is high priority? Status: ' + isHighPriority);

    if(!isHighPriority) {
        const isGenericXboxSupportWorker = !!workerLabels["Support_XBOX"];
        context.log('Worker provides general xbox support? Status: ' + isGenericXboxSupportWorker);

        score = isGenericXboxSupportWorker ? 100 : 1;

    } else {
        const workerSupportsHighPriorityJob = !!workerLabels["HighPrioritySupport"];
        context.log('Worker provides high priority support? Status: ' + workerSupportsHighPriorityJob);

        if(!workerSupportsHighPriorityJob) {
            score = 1;
        } else {
            const key = `Support_${jobLabels["ConsoleType"]}`;
            
            const workerSpecializeInConsoleType = !!workerLabels[key];
            context.log(`Worker specializes in consoleType: ${jobLabels["ConsoleType"]} ? Status: ${workerSpecializeInConsoleType}`);

            score = workerSpecializeInConsoleType ? 200 : 100;
        }
    }
    context.log('Final score of worker: ' + score);

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: score
    };
}
```

Output for **Worker 1**

```markdown
200
```

With the aforementioned implementation, for the given job we'll get the following scores for workers:

| Worker | Score |
|--------|-------|
| Worker 1 | 200 |
| Worker 2 | 200 |
| Worker 3 | 1 |

## Distribute offers based on best worker mode

Now that the Azure function app is ready, let us create an instance of **BestWorkerDistribution** mode using Router SDK.

```csharp
    // ----- initialize router client
    // Setup Distribution Policy
    var bestWorkerDistributionMode = new BestWorkerMode(
        scoringRule: new AzureFunctionRule(
            functionAppUrl: "<insert function url>");

    var distributionPolicy = await client.SetDistributionPolicyAsync(
        id: "BestWorkerDistributionMode",
        mode: bestWorkerDistributionMode,
        name: "XBox hardware support distribution",
        offerTTL: TimeSpan.FromMinutes(5));

    // Setup Queue
    var queue = await client.SetQueueAsync(
        id: "XBox_Hardware_Support_Q",
        distributionPolicyId: distributionPolicy.Value.Id,
        name: "XBox Hardware Support Queue");

    // Setup Channel
    var channel = await client.SetChannelAsync("Xbox_Chat_Channel");

    // Create workers

    var worker1Labels = new LabelCollection()
    {
        ["HighPrioritySupport"] = true,
        ["HardwareSupport"] = true,
        ["Support_XBOX_SERIES_X"] = true,
        ["English"] = 10,
        ["ChatSupport"] = true,
        ["XboxSupport"] = true
    };
    var worker1 = await client.RegisterWorkerAsync(
        id: "Worker_1",
        totalCapacity: 100,
        queueIds: new[] {queue.Value.Id},
        labels: worker1Labels,
        channelConfigurations: new[] {new ChannelConfiguration(channel.Value.Id, 10)});

    var worker2Labels = new LabelCollection()
    {
        ["HighPrioritySupport"] = true,
        ["HardwareSupport"] = true,
        ["Support_XBOX_SERIES_X"] = true,
        ["Support_XBOX_SERIES_S"] = true,
        ["English"] = 8,
        ["ChatSupport"] = true,
        ["XboxSupport"] = true
    };
    var worker2 = await client.RegisterWorkerAsync(
        id: "Worker_2",
        totalCapacity: 100,
        queueIds: new[] { queue.Value.Id },
        labels: worker2Labels,
        channelConfigurations: new[] { new ChannelConfiguration(channel.Value.Id, 10) });

    var worker3Labels = new LabelCollection()
    {
        ["HighPrioritySupport"] = false,
        ["HardwareSupport"] = true,
        ["Support_XBOX"] = true,
        ["English"] = 7,
        ["ChatSupport"] = true,
        ["XboxSupport"] = true
    };
    var worker3 = await client.RegisterWorkerAsync(
        id: "Worker_3",
        totalCapacity: 100,
        queueIds: new[] { queue.Value.Id },
        labels: worker3Labels,
        channelConfigurations: new[] { new ChannelConfiguration(channel.Value.Id, 10) });

    // Create Job
    var jobLabels = new LabelCollection()
    {
        ["CommunicationType"] = "Chat",
        ["IssueType"] = "XboxSupport",
        ["Language"] = "en",
        ["HighPriority"] = true,
        ["SubIssueType"] = "ConsoleMalfunction",
        ["ConsoleType"] = "XBOX_SERIES_X",
        ["Model"] = "XBOX_SERIES_X_1TB"
    };
    var workerSelectors = new List<LabelSelector>()
    {
        new LabelSelector("English", LabelOperator.GreaterThanEqual, 7),
        new LabelSelector("ChatSupport", LabelOperator.Equal, true),
        new LabelSelector("XboxSupport", LabelOperator.Equal, true)
    };
    var job = await client.CreateJobAsync(
        channelId: channel.Value.Id,
        queueId: queue.Value.Id,
        priority: 100,
        channelReference: "ChatChannel",
        labels: jobLabels,
        workerSelectors: workerSelectors);

    var getJob = await client.GetJobAsync(job.Value.Id);
    Console.WriteLine(getJob.Value.Assignments.Select(assignment => assignment.Value.WorkerId).First());
```

Output

```markdown
Worker_1 // or Worker_2

Since both workers, Worker_1 and Worker_2, get the same score of 200,
the worker who has been idle the longest will get the first offer.
```
