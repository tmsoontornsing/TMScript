# 🌦️ TMScript Demo: Multi-Platform Weather Automation

## 📜 Script: `demoFeatures.tms`

### 🎯 Scenario
A comprehensive demonstration of TMScript features that:
- Fetches real-time weather data
- Uses AI for activity suggestions
- Posts updates to WordPress
- Notifies via Slack
- Schedules reminders

### 🧩 Complete Script

```tmscript
# Variables for configuration
let $city = "New York";
let $cacheFile = "weather_cache.txt";
let $cacheExpiry = 3600;  # Cache expiry time in seconds (1 hour)

# API keys
let $openWeatherKey = "YOUR_OPENWEATHER_API_KEY";
let $wordpressKey = "YOUR_WORDPRESS_API_KEY";
let $slackToken = "YOUR_SLACK_TOKEN";

# Connect to platforms
connect wordpress url="my-site.com", key=$wordpressKey;
connect slack token=$slackToken;

# Function: Fetch and cache weather data
define fetchWeather city, apiKey:
    let $cacheValid = #checkCache path=$cacheFile, expiry=$cacheExpiry;
    if $cacheValid:
        let $weatherData = #readCache path=$cacheFile;
    else:
        let $apiUrl = "https://api.openweathermap.org/data/2.5/weather?q=" + city + "&appid=" + apiKey;
        let $response = #httpGet url=$apiUrl;
        let $weatherData = $response.body;
        #writeCache path=$cacheFile, data=$weatherData;

    return $weatherData;

# Function: Parse and format weather data
define formatWeather weatherData:
    let $temperature = weatherData.main.temp - 273.15;  # Convert from Kelvin to Celsius
    let $description = weatherData.weather[0].description;
    return "The temperature in " + $city + " is " + $temperature + "°C with " + $description + ".";

# Fetch weather data
let $weatherData = fetchWeather city=$city, apiKey=$openWeatherKey;
let $formattedWeather = formatWeather weatherData=$weatherData;

# Use AI to suggest an activity based on the weather
let $aiPrompt = "Suggest an activity for the following weather: " + $formattedWeather;
let $activitySuggestion = #askAI query=$aiPrompt;

# Post weather and activity to WordPress
wordpress.createPost title="Today's Weather Update", content=$formattedWeather + "\n\nSuggested Activity: " + $activitySuggestion;

# Notify a Slack channel
let $slackMessage = "Weather Update:\n" + $formattedWeather + "\nSuggested Activity: " + $activitySuggestion;
slack.sendMessage channel="#updates", message=$slackMessage;

# Loop: Notify multiple platforms
repeat count=2:
    say message="Notification sent to platform " + $counter;

# Schedule a reminder based on the weather
if $formattedWeather includes "rain":
    @setReminder message="Carry an umbrella!", time="08:00";
else:
    @setReminder message="Enjoy your day!", time="08:00";

# Custom command: Automate this workflow daily
addCommand name="dailyWeatherUpdate":
    let $weatherData = fetchWeather city=$city, apiKey=$openWeatherKey;
    let $formattedWeather = formatWeather weatherData=$weatherData;
    let $activitySuggestion = #askAI query="Suggest an activity for the following weather: " + $formattedWeather;

    wordpress.createPost title="Daily Weather Update", content=$formattedWeather + "\n\nActivity: " + $activitySuggestion;
    slack.sendMessage channel="#updates", message="Weather and activity posted for today!";
    @setReminder message="Check weather update posted on WordPress", time="09:00";

# Execute daily workflow
dailyWeatherUpdate;
```

## 🔍 Feature Breakdown

### 1. 📊 Variables
- `$city`: Location for weather data
- `$cacheFile`: Path for caching weather information
- `$cacheExpiry`: Cache duration control

### 2. 🔀 Conditions
- Weather-based reminder logic
- Conditional caching mechanism

### 3. 🔁 Loops
- `repeat` for multi-platform notifications
- Demonstrates iterative execution

### 4. 🧩 Functions
- `fetchWeather`: Retrieve and cache weather data
- `formatWeather`: Parse and format weather information

### 5. 🌐 Platform Connectors
- WordPress: Create and publish posts
- Slack: Send real-time notifications

### 6. 🤖 AI Integration
- Generate activity suggestions based on weather
- Dynamic content creation

### 7. ⏰ Task Automation
- `@setReminder` for scheduling context-aware reminders
- `dailyWeatherUpdate` custom command for workflow automation

## 🚀 Key Advantages

- **Comprehensive Feature Demonstration**
- **Real-World Practical Scenario**
- **Modular and Extensible Design**
- **AI-Powered Dynamic Content**

## 🔮 Potential Enhancements
- Add robust error handling
- Implement more complex AI interactions
- Create more platform connectors

## 📋 Next Steps
1. Design interpreter logic
2. Implement error handling
3. Expand connector support
