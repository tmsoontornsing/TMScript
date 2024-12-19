# 🚀 TMScript YAML: Workflow Automation Patterns

## Overview of YAML-Driven Workflows

TMScript leverages YAML to create dynamic, flexible automation workflows across various scenarios:
- Event-triggered actions
- API sequence execution
- Scheduled tasks
- Conditional workflow management

## 1. Event-Driven Temperature Notifications

### Workflow Concept
Automatically trigger notifications and posts based on temperature conditions.

```yaml
---yaml
events:
  - trigger: "temperature > 30"
    actions:
      - command: "notifySlack"
        params:
          message: "It's a hot day!"
      - command: "postToWordPress"
        params:
          title: "Heatwave Alert"
          content: "Stay cool! It's over 30°C today."
  - trigger: "temperature <= 30"
    actions:
      - command: "postToWordPress"
        params:
          title: "Mild Weather Update"
          content: "It's a pleasant day below 30°C."
---
```

### Execution Logic
```tmscript
let $temperature = fetchWeather city="New York";

repeat count={events.length}:
    let $event = {events[$counter]};
    if evaluateCondition($event.trigger, temperature=$temperature):
        repeat count={$event.actions.length}:
            let $action = $event.actions[$counter];
            executeCommand name=$action.command, params=$action.params;
```

## 2. API Chaining Workflow

### Workflow Concept
Execute a sequence of dependent API calls for user management.

```yaml
---yaml
apiWorkflow:
  steps:
    - name: "Fetch User"
      endpoint: "https://api.example.com/users/{userId}"
      method: "GET"
      params:
        userId: 123
    - name: "Update User"
      endpoint: "https://api.example.com/users/{userId}"
      method: "POST"
      params:
        userId: 123
        data:
          isActive: true
    - name: "Notify User"
      endpoint: "https://api.example.com/notifications"
      method: "POST"
      params:
        userId: 123
        message: "Your account has been updated."
---
```

### Execution Logic
```tmscript
repeat count={apiWorkflow.steps.length}:
    let $step = {apiWorkflow.steps[$counter]};
    let $response = #httpRequest 
        url=$step.endpoint, 
        method=$step.method, 
        params=$step.params;
    say message="Step " + $step.name + " completed. Response: " + $response;
```

## 3. Multi-Task Scheduler

### Workflow Concept
Schedule and execute multiple tasks at specific times.

```yaml
---yaml
scheduledTasks:
  - name: "Morning Reminder"
    time: "08:00"
    actions:
      - command: "notifySlack"
        params:
          channel: "#general"
          message: "Good morning! Start your day right."
  - name: "Daily Weather Update"
    time: "12:00"
    actions:
      - command: "fetchWeather"
        params:
          city: "New York"
      - command: "postToWordPress"
        params:
          title: "Midday Weather"
          content: "Here's the latest weather update."
---
```

### Execution Logic
```tmscript
repeat count={scheduledTasks.length}:
    let $task = {scheduledTasks[$counter]};
    @setReminder message=$task.name, time=$task.time;

    if timeNow() == $task.time:
        repeat count={$task.actions.length}:
            let $action = $task.actions[$counter];
            executeCommand name=$action.command, params=$action.params;
```

## 4. Conditional Workflow Execution

### Workflow Concept
Dynamically enable or disable tasks based on configuration flags.

```yaml
---yaml
workflow:
  enableSlack: true
  enableWordPress: false
  tasks:
    - name: "Fetch Weather"
      command: "fetchWeather"
      params:
        city: "London"
    - name: "Post to Slack"
      command: "notifySlack"
      params:
        message: "Weather update: {environment.city}"
---
```

### Execution Logic
```tmscript
repeat count={workflow.tasks.length}:
    let $task = {workflow.tasks[$counter]};
    if ($task.command == "notifySlack" and {workflow.enableSlack}) or 
       ($task.command == "postToWordPress" and {workflow.enableWordPress}):
        executeCommand name=$task.command, params=$task.params;
```

## Key Benefits of YAML Workflows

| Benefit | Description | Impact |
|---------|-------------|--------|
| 🔄 Reusability | Configurations can be easily reused | Reduced redundancy |
| 🧩 Modularity | Separate data from logic | Improved maintainability |
| 🚀 Dynamic Execution | Conditional and sequential workflows | Flexible automation |

## Technology Stack

![YAML](https://img.shields.io/badge/YAML-v1.2-blue)
![Node.js](https://img.shields.io/badge/Node.js-Compatible-green)
![TypeScript](https://img.shields.io/badge/TypeScript-Support-blue)

---

🌈 **Empowering Automation Through Intelligent Configuration**
