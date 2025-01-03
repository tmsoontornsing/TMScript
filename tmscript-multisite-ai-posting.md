# TMScript: Multi-Platform News Sharing Automation

## Overview
A complete TMScript that fetches news, summarizes it into different formats, and posts it to 5 platforms (WordPress, Facebook, Twitter, Instagram, TikTok). The script includes AI-powered prompt logic for dynamic content creation tailored to each platform.

## Configuration

```yaml
config:
  news:
    url: "https://www.nytimes.com/"
    apiKey: "NEWS_API_KEY"
  wordpress:
    url: "https://my-wordpress-site.com"
    apiKey: "WORDPRESS_API_KEY"
  socialMedia:
    facebook:
      apiKey: "FACEBOOK_API_KEY"
    twitter:
      apiKey: "TWITTER_API_KEY"
    instagram:
      apiKey: "INSTAGRAM_API_KEY"
    tiktok:
      apiKey: "TIKTOK_API_KEY"
  outputFolder: "./news_summaries/"
aiPrompts:
  summarize:
    base: "Summarize the following text:"
    maxWords: 100
  facebookPost:
    base: "Create a Facebook post for the following content:"
  twitterPost:
    base: "Summarize for Twitter (max 280 characters):"
  instagramPost:
    base: "Create an Instagram caption for the following content:"
  tiktokPost:
    base: "Create engaging TikTok content for the following content:"
```

## Main Script

```tmscript
# Step 1: Fetch the latest news article
let $newsContent = #httpGet url="{config.news.url}/api/latest-news", apiKey="{config.news.apiKey}";
let $article = extractNews content=$newsContent;

# Step 2: Generate content for each platform using AI prompts
let $summaryPrompt = generatePrompt type="summarize";
let $summary = #askAI prompt=$summaryPrompt, text=$article.body;

let $facebookPrompt = generatePrompt type="facebookPost";
let $facebookContent = #askAI prompt=$facebookPrompt, text=$article.body;

let $twitterPrompt = generatePrompt type="twitterPost";
let $twitterContent = #askAI prompt=$twitterPrompt, text=$article.body;

let $instagramPrompt = generatePrompt type="instagramPost";
let $instagramContent = #askAI prompt=$instagramPrompt, text=$article.body;

let $tiktokPrompt = generatePrompt type="tiktokPost";
let $tiktokContent = #askAI prompt=$tiktokPrompt, text=$article.body;

# Step 3: Convert content to markdown and save locally
define convertToMarkdown title, summary, facebook, twitter, instagram, tiktok:
    return "# " + title + "\n\n" +
           "## Summary\n" + summary + "\n\n" +
           "## Facebook Content\n" + facebook + "\n\n" +
           "## Twitter Content\n" + twitter + "\n\n" +
           "## Instagram Content\n" + instagram + "\n\n" +
           "## TikTok Content\n" + tiktok + "\n";

let $markdownContent = convertToMarkdown title=$article.title, 
    summary=$summary,
    facebook=$facebookContent,
    twitter=$twitterContent,
    instagram=$instagramContent,
    tiktok=$tiktokContent;

let $filePath = "{config.outputFolder}" + $article.slug + ".md";
#writeFile path=$filePath, content=$markdownContent;

say message="Article saved to local folder: " + $filePath;

# Step 4: Post to WordPress
connect wordpress url="{config.wordpress.url}", key="{config.wordpress.apiKey}";
let $wordpressPost = wordpress.createPost title=$article.title, content=$markdownContent;
let $postUrl = $wordpressPost.url;
say message="Article posted to WordPress: " + $postUrl;

# Step 5: Share the post on all social media platforms
connect facebook apiKey="{config.socialMedia.facebook.apiKey}";
facebook.post content="Check out our latest article: " + $postUrl + " - " + $facebookContent;

connect twitter apiKey="{config.socialMedia.twitter.apiKey}";
twitter.post content="Latest update: " + $postUrl + " - " + $twitterContent;

connect instagram apiKey="{config.socialMedia.instagram.apiKey}";
instagram.post content=$instagramContent + "\nRead more: " + $postUrl;

connect tiktok apiKey="{config.socialMedia.tiktok.apiKey}";
tiktok.post content=$tiktokContent + "\nRead full article here: " + $postUrl;

say message="Workflow complete. Article shared across WordPress and all social media platforms.";
```

## Helper Functions

### Generate Prompt

```tmscript
define generatePrompt type, params:
    let $promptBase = $config.aiPrompts[type].base;

    repeat count=$params.keys.length:
        let $key = $params.keys[$counter];
        $promptBase = $promptBase.replace("{" + $key + "}", $params[$key]);

    return $promptBase;
```

### Extract News

```tmscript
define extractNews content:
    let $articles = content.articles;
    return $articles[0]; # Assuming the latest article is the first
```

### Convert to Markdown

```tmscript
define convertToMarkdown title, summary, facebook, twitter, instagram, tiktok:
    return "# " + title + "\n\n" +
           "## Summary\n" + summary + "\n\n" +
           "## Facebook Content\n" + facebook + "\n\n" +
           "## Twitter Content\n" + twitter + "\n\n" +
           "## Instagram Content\n" + instagram + "\n\n" +
           "## TikTok Content\n" + tiktok + "\n";
```

## Error Handling

### Missing News Content

```tmscript
if $newsContent == null:
    say message="Failed to fetch news content.";
    exit;
```

### Failed Social Media Post

```tmscript
try:
    facebook.post content="Check out this article: " + $postUrl + " - " + $facebookContent;
catch error:
    say message="Failed to share on Facebook. Error: " + error;
```

## How It Works

1. **YAML Configuration**
   - Centralized Management:
     - news section: API details for fetching news
     - wordpress section: WordPress site URL and API key
     - socialMedia section: API keys for Facebook, Twitter, Instagram, and TikTok
     - aiPrompts section: AI prompts for generating platform-specific content

2. **Fetch News**
   - Uses #httpGet to retrieve the latest article from the news API
   - The extractNews function parses the article title, body, and slug

3. **Generate AI Content**
   - Uses #askAI with prompts defined in the aiPrompts section to:
     - Summarize the article
     - Create platform-specific content for Facebook, Twitter, Instagram, and TikTok

4. **Save to Markdown**
   - Converts the summarized content into markdown format using convertToMarkdown
   - Saves the file locally for archival or manual review

5. **Post to WordPress**
   - Publishes the markdown content to a WordPress site using the WordPress API

6. **Share to Social Media**
   - Shares the WordPress post URL with platform-specific captions/content to:
     - Facebook
     - Twitter
     - Instagram
     - TikTok

## Next Steps

1. **Extend Sharing Logic**
   - Add scheduling for posts using @setReminder

2. **Support Multiple Articles**
   - Modify the script to handle a batch of articles

3. **Analytics Integration**
   - Track engagement metrics for shared posts
