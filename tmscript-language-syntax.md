# 📘 TMScript Language Syntax Documentation

## 1. 🌟 Overview

TMScript is a modern scripting language designed for:
- Task automation
- AI integration
- Platform API interactions
- Human-readable syntax

## 2. 🔤 Basic Syntax

### 2.1 Comments
```tmscript
# This is a single-line comment
say message="Hello, world!";
```

### 2.2 Variables
```tmscript
# Variable declaration
let $userName = "Alice";
let $temperature = 25;

# Variable usage
say message="Hello, " + $userName + "!";
```

### 2.3 Data Types

| Type | Examples | Description |
|------|----------|-------------|
| String | `"Hello"` | Text data |
| Number | `42`, `3.14` | Numeric values |
| Boolean | `true`, `false` | Logical values |
| List | `[1, 2, 3]` | Ordered collection |
| Object | `{name: "Alice", age: 30}` | Key-value pairs |

**Example:**
```tmscript
let $user = {name: "Alice", age: 30};
let $numbers = [1, 2, 3, 4];
```

### 2.4 Custom Symbols

| Symbol | Purpose |
|--------|---------|
| `$` | Variables |
| `@` | Task commands (reminders) |
| `#` | AI-related commands |

## 3. 🔀 Control Flow

### 3.1 Conditions
```tmscript
# Simple condition
if $temperature > 30:
    say message="It's hot!";
else:
    say message="It's cool.";

# Nested conditions
if $temperature > 30:
    if $humidity > 50:
        say message="It's hot and humid!";
```

### 3.2 Loops

#### Fixed Iterations
```tmscript
repeat count=5:
    say message="Loop iteration!";
```

#### Conditional Loop
```tmscript
let $i = 0;
while $i < 5:
    say message="Count: " + $i;
    let $i = $i + 1;
```

## 4. 🧩 Functions

### Function Definition
```tmscript
# Function with default parameter
define greet name="Guest":
    say message="Hello, " + name;

# Calling the function
greet;                 # Output: Hello, Guest
greet name="Alice";   # Output: Hello, Alice
```

## 5. 🤖 Built-In Commands

### 5.1 Task Automation
```tmscript
# Set a reminder
@setReminder message="Meeting with Bob", time="14:00";
```

### 5.2 AI Integration
```tmscript
# AI Query
let $response = #askAI query="What's the weather like?";

# Translation
#translate text="Good morning", to="French";

# Summarization
let $summary = #summarize text="Long article", maxWords=50;
```

### 5.3 Platform Connectors
```tmscript
# WordPress
connect wordpress url="my-site.com", key="API_KEY";
wordpress.createPost title="Update", content="It's sunny!";

# Shopify
connect shopify url="my-store.com", key="API_KEY";
shopify.addProduct name="T-shirt", price=25, stock=100;

# Slack
connect slack token="SLACK_TOKEN";
slack.sendMessage channel="#general", message="Hello, team!";
```

## 6. 🛡️ Error Handling

```tmscript
try:
    let $response = #askAI query="Capital of France?";
catch error:
    say message="AI query failed: " + error;
```

## 7. 🚀 Advanced Features

### 7.1 Custom Commands
```tmscript
addCommand name="morningRoutine":
    say message="Good morning!";
    @setReminder message="Exercise", time="06:30";

# Execute custom command
morningRoutine;
```

### 7.2 Command Chaining
```tmscript
say message="Hello!"; 
@setReminder message="Call Mom", time="17:00"; 
#askAI query="Best dinner recipe?";
```

## 8. 💡 Best Practices

1. Use clear, meaningful variable names
2. Always implement error handling
3. Modularize repetitive logic
4. Use command chaining sparingly
5. Keep scripts readable and maintainable

## 9. 📝 Example Script

```tmscript
let $city = "New York";
let $openWeatherKey = "YOUR_API_KEY";

connect wordpress url="my-site.com", key="WORDPRESS_KEY";

define fetchWeather city, apiKey:
    let $url = "https://api.openweathermap.org/data/2.5/weather?q=" + city + "&appid=" + apiKey;
    let $response = #httpGet url=$url;
    return $response.body;

let $weather = fetchWeather city=$city, apiKey=$openWeatherKey;
wordpress.createPost title="Weather Update", content=$weather;
```

---

🌈 **TMScript: Simplifying Automation, Empowering Creativity**
