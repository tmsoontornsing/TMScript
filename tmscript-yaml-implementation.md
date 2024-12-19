# 🔧 TMScript YAML Implementation Documentation

## 🌟 Overview

TMScript introduces robust YAML configuration integration to:
- Simplify script creation
- Enable structured settings
- Support flexible configuration management

## 1. Configuration Types

### 1.1 Inline YAML Configuration
```tmscript
---yaml
environment:
  city: "New York"
  apiKeys:
    openWeather: "YOUR_API_KEY"
---

say message="Fetching weather for {environment.city}.";
```

### 1.2 External YAML Configuration
```tmscript
# Load external configuration
loadConfig file="config.yaml";

say message="Fetching weather for {environment.city}.";
```

## 2. Key Features

### 2.1 Direct Value Access
```tmscript
say message="Using API key: {apiKeys.openWeather}";
```

### 2.2 Fallback Mechanisms
```tmscript
say message="API Key: {apiKeys.openWeather|NoKeyProvided}";
```

### 2.3 Nested Key Support
```tmscript
let $apiKey = "{apiKeys.openWeather}";
```

## 3. Advanced Configuration Example

### Inline YAML Workflow
```tmscript
---yaml
environment:
  city: "New York"
  apiKeys:
    openWeather: "YOUR_API_KEY"
workflow:
  tasks:
    - name: "Fetch Weather"
      command: "fetchWeather"
      params:
        city: "London"
    - name: "Notify Slack"
      command: "notifySlack"
      params:
        message: "Weather Update"
---

# Dynamic task execution
repeat count={tasks.length}:
    let $task = {tasks[$counter]};
    executeCommand name=$task.command, params=$task.params;
```

## 4. Implementation Architecture

### 4.1 YAML Parsing Process
1. Detect YAML block
2. Parse using js-yaml
3. Populate `$config` object
4. Resolve placeholders during execution

### 4.2 Placeholder Resolution
```javascript
function resolvePlaceholder(placeholder) {
  // {key|fallback} syntax support
  const [key, fallback] = placeholder.split('|');
  return $config.get(key) || fallback;
}
```

## 5. Command Reference

| Command | Description | Example |
|---------|-------------|---------|
| `---yaml` | Embed YAML configuration | `---yaml environment: {...}---` |
| `loadConfig` | Load external YAML | `loadConfig file="config.yaml"` |

## 6. Best Practices

- 🔒 Use fallbacks for optional configurations
- 📂 Organize configurations hierarchically
- 🛡️ Avoid hardcoding sensitive information
- 🔄 Leverage external YAML for reusable configs

## 7. Error Handling

```
Error: Invalid YAML structure in config.yaml.
Suggestion: Check indentation and syntax.
```

## 8. Advantages

- 📖 Improved Readability
- 🔀 Enhanced Portability
- 🧩 Simplified Maintenance
- 📈 Scalable Workflow Definitions

## 9. Technology Stack

![YAML](https://img.shields.io/badge/YAML-v1.2-blue)
![Node.js](https://img.shields.io/badge/Node.js-Compatible-green)
![TypeScript](https://img.shields.io/badge/TypeScript-Support-blue)

## 10. Future Roadmap

- 🌐 Remote configuration loading
- 🔍 Enhanced schema validation
- 🤖 AI-assisted configuration generation

---

🌈 **Empowering Automation with Intelligent Configuration**
