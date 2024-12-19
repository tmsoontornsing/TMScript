# 🧩 TMScript YAML Configuration Guide

## 🌟 Overview: YAML Integration

### Why YAML?
- 📝 Human-readable configuration
- 🔧 Centralized settings management
- 🚀 Enhanced script flexibility
- 🌐 Environment-specific configurations

## 1. Configuration Examples

### Basic Environment Configuration
```yaml
environment:
  city: "New York"
  apiKeys:
    openWeather: "YOUR_OPENWEATHER_API_KEY"
    wordpress: "YOUR_WORDPRESS_API_KEY"
    slack: "YOUR_SLACK_TOKEN"
```

### Workflow Control
```yaml
workflow:
  postToWordpress: true
  notifySlack: false
  cities:
    - "New York"
    - "San Francisco"
    - "London"
```

## 2. TMScript YAML Integration

### Loading Configuration
```tmscript
# Load YAML configuration
loadConfig file="config.yaml";

# Access configuration values
let $city = $config.environment.city;
let $apiKey = $config.environment.apiKeys.openWeather;

# Conditional workflow based on configuration
if $config.workflow.postToWordpress:
    connect wordpress url="my-site.com", key=$config.environment.apiKeys.wordpress;
    wordpress.createPost 
        title="Weather Update", 
        content="Today's weather in " + $city;
```

## 3. Implementation Details

### YAML Parsing (Node.js)
```javascript
const yaml = require('js-yaml');
const fs = require('fs');

function loadConfig(filePath) {
  try {
    const fileContent = fs.readFileSync(filePath, 'utf8');
    return yaml.load(fileContent);
  } catch (error) {
    console.error('Configuration load error:', error);
    return {};
  }
}
```

## 4. Key Benefits

| Feature | Description | Impact |
|---------|-------------|--------|
| 🔐 Centralized Secrets | Manage API keys externally | Enhanced security |
| 🔄 Environment Flexibility | Switch configs easily | Improved portability |
| 📊 Dynamic Configurations | Control script behavior | Greater adaptability |

## 5. Error Handling

### YAML Parsing Errors
```
Error: Invalid YAML structure in config.yaml at line 3.
Suggestion: Ensure correct indentation and syntax.
```

## 6. Best Practices

- 🛡️ Never commit sensitive keys to version control
- 🔒 Use environment-specific configuration files
- 📝 Document configuration structure
- 🧪 Validate YAML before script execution

## 7. Advanced Features

### Dynamic YAML Updates
```tmscript
# Update configuration during runtime
$config.environment.city = "San Francisco";
```

### Fallback Defaults
```tmscript
# Use default if config property missing
let $defaultCity = $config.environment.city || "New York";
```

## 8. Future Roadmap

- 🚀 Remote YAML configuration loading
- 🔍 YAML schema validation
- 🌐 Environment variable interpolation
- 🤖 AI-assisted configuration generation

## 9. Compatibility

![Node.js](https://img.shields.io/badge/Node.js-Compatible-green?logo=nodedotjs)
![YAML](https://img.shields.io/badge/YAML-v1.2-blue)
![TypeScript](https://img.shields.io/badge/TypeScript-Support-blue?logo=typescript)

---

🌈 **Simplifying Configuration, Empowering Automation**
