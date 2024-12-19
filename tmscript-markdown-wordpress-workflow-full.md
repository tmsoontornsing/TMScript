# TMScript for Managing Multiple Markdown Uploads and WordPress Posts

## Script Requirements

### 1. UI for Markdown Uploads
- A user interface to upload markdown documents

### 2. Multiple WordPress Sites Configuration
- YAML or inline configuration to define site URLs and API keys

### 3. Posting Workflow
- Iterate over all markdown files and post them to all configured WordPress sites

### 4. Verification
- Check if each document was successfully posted to all WordPress sites

## Complete TMS Script

### WordPress Sites Configuration
```yaml
---yaml
wordpressSites:
  - name: "Tech Blog"
    url: "https://techblog.example.com"
    apiKey: "TECH_BLOG_API_KEY"
  - name: "Travel Blog"
    url: "https://travelblog.example.com"
    apiKey: "TRAVEL_BLOG_API_KEY"
---
```

### Full TMScript Implementation
```javascript
# UI: Upload multiple markdown documents
ui uploadMarkdown files=$uploadedDocs;
say message="Successfully uploaded " + $uploadedDocs.length + " markdown files.";

# Validate uploaded files
if $uploadedDocs.length == 0:
    say message="No markdown files uploaded. Exiting workflow.";
    exit;

# Iterate over each uploaded markdown document
repeat count=$uploadedDocs.length:
    let $doc = $uploadedDocs[$counter];
    let $content = readFile path=$doc.path;
    let $title = extractTitle content=$content;

    say message="Processing document: " + $title;

    # Post the document to each WordPress site
    repeat count={wordpressSites.length}:
        let $site = {wordpressSites[$counter]};
        say message="Posting to site: " + $site.name + " (" + $site.url + ")";

        # Connect to WordPress and post the markdown content
        connect wordpress url=$site.url, key=$site.apiKey;
        try:
            let $response = wordpress.createPost title=$title, content=$content;
            say message="Successfully posted to " + $site.name + ". Post ID: " + $response.postId;
        catch error:
            say message="Failed to post to " + $site.name + ". Error: " + error;

# Verification: Check if all documents were posted
say message="Verifying posts on all WordPress sites...";
repeat count=$uploadedDocs.length:
    let $doc = $uploadedDocs[$counter];
    let $title = extractTitle content=readFile path=$doc.path;

    repeat count={wordpressSites.length}:
        let $site = {wordpressSites[$counter]};
        try:
            let $post = wordpress.getPostByTitle title=$title;
            if $post == null:
                say message="Post not found for " + $title + " on site: " + $site.name;
            else:
                say message="Post verified on site: " + $site.name + ". Post ID: " + $post.postId;
        catch error:
            say message="Verification failed for site: " + $site.name + ". Error: " + error;

say message="Workflow complete. All markdown documents processed.";
```

### Full JavaScript Implementation for comparison
```javascript
const fs = require('fs');
const path = require('path');
const axios = require('axios');
const readline = require('readline');

// Configuration: Define WordPress Sites
const wordpressSites = [
  { name: "Tech Blog", url: "https://techblog.example.com", apiKey: "TECH_BLOG_API_KEY" },
  { name: "Travel Blog", url: "https://travelblog.example.com", apiKey: "TRAVEL_BLOG_API_KEY" },
];

// Simulate UI for uploading markdown documents
async function uploadMarkdown() {
  // Mocking file upload. Replace this with actual UI logic in a real application.
  const mockFiles = [
    { name: "Post1.md", path: "./Post1.md" },
    { name: "Post2.md", path: "./Post2.md" },
  ];
  console.log(`Successfully uploaded ${mockFiles.length} markdown files.`);
  return mockFiles;
}

// Read file content
function readFile(filePath) {
  return fs.readFileSync(filePath, 'utf8');
}

// Extract title from markdown content
function extractTitle(content) {
  const lines = content.split('\n');
  for (let line of lines) {
    if (line.startsWith('# ')) {
      return line.replace('# ', '').trim();
    }
  }
  return 'Untitled';
}

// Post markdown content to WordPress
async function postToWordPress(site, title, content) {
  try {
    const response = await axios.post(
      `${site.url}/wp-json/wp/v2/posts`,
      { title, content, status: 'publish' },
      { headers: { Authorization: `Bearer ${site.apiKey}` } }
    );
    console.log(`Successfully posted to ${site.name}. Post ID: ${response.data.id}`);
    return response.data.id;
  } catch (error) {
    console.error(`Failed to post to ${site.name}: ${error.message}`);
    return null;
  }
}

// Verify if a post exists on WordPress
async function verifyPostOnWordPress(site, title) {
  try {
    const response = await axios.get(`${site.url}/wp-json/wp/v2/posts?search=${encodeURIComponent(title)}`, {
      headers: { Authorization: `Bearer ${site.apiKey}` },
    });
    if (response.data.length > 0) {
      console.log(`Post verified on site: ${site.name}. Post ID: ${response.data[0].id}`);
      return true;
    } else {
      console.log(`Post not found for "${title}" on site: ${site.name}`);
      return false;
    }
  } catch (error) {
    console.error(`Verification failed for site: ${site.name}: ${error.message}`);
    return false;
  }
}

// Main workflow
async function main() {
  // Step 1: Upload markdown files
  const uploadedDocs = await uploadMarkdown();

  // Step 2: Validate uploaded files
  if (uploadedDocs.length === 0) {
    console.log('No markdown files uploaded. Exiting workflow.');
    return;
  }

  // Step 3: Iterate over each uploaded markdown document
  for (const doc of uploadedDocs) {
    const content = readFile(doc.path);
    const title = extractTitle(content);
    console.log(`Processing document: ${title}`);

    // Step 4: Post the document to each WordPress site
    for (const site of wordpressSites) {
      console.log(`Posting to site: ${site.name} (${site.url})`);
      await postToWordPress(site, title, content);
    }
  }

  // Step 5: Verification: Check if all documents were posted
  console.log('Verifying posts on all WordPress sites...');
  for (const doc of uploadedDocs) {
    const content = readFile(doc.path);
    const title = extractTitle(content);

    for (const site of wordpressSites) {
      await verifyPostOnWordPress(site, title);
    }
  }

  console.log('Workflow complete. All markdown documents processed.');
}

// Execute the workflow
main().catch((error) => console.error(`Workflow failed: ${error.message}`));
```

## Workflow Explanation

### 1. YAML Configuration
Define WordPress Sites:
- The `wordpressSites` block configures site names, URLs, and API keys

```yaml
wordpressSites:
  - name: "Tech Blog"
    url: "https://techblog.example.com"
    apiKey: "TECH_BLOG_API_KEY"
  - name: "Travel Blog"
    url: "https://travelblog.example.com"
    apiKey: "TRAVEL_BLOG_API_KEY"
```

### 2. Upload Markdown Files
- The `ui uploadMarkdown` command opens a user interface for file uploads
- The uploaded file paths are stored in `$uploadedDocs`

### 3. Read and Extract Content
- Use the `readFile` command to load the markdown content
- Use a helper function `extractTitle` to extract the title from the markdown document

### 4. Post to WordPress Sites
- Iterate over all documents and all WordPress sites
- For each combination, post the markdown content using `wordpress.createPost`

### 5. Verify Posts
- Validate if each document was successfully posted on all WordPress sites
- Search for posts with matching titles

## Helper Functions

### Extract Title from Markdown
```tmscript
define extractTitle content:
    let $lines = content.split("\n");
    repeat count=$lines.length:
        let $line = $lines[$counter];
        if $line.startsWith("# "):
            return $line.replace("# ", "");
    return "Untitled";
```

### Read File Content
```tmscript
define readFile path:
    # Reads the content of a file and returns it as a string
    return #readFile path=path;
```

## Error Handling

### 1. File Upload Errors
```tmscript
if $uploadedDocs.length == 0:
    say message="No markdown files uploaded. Exiting workflow.";
    exit;
```

### 2. WordPress Posting Errors
```tmscript
try:
    let $response = wordpress.createPost title=$title, content=$content;
catch error:
    say message="Failed to post to " + $site.name + ". Error: " + error;
```

### 3. Post Verification Errors
```tmscript
try:
    let $post = wordpress.getPostByTitle title=$title;
    if $post == null:
        say message="Post not found for " + $title + " on site: " + $site.name;
catch error:
    say message="Verification failed for site: " + $site.name + ". Error: " + error;
```

## Key Features in This Script

1. **UI-Driven File Uploads**
   - Easy user interface for uploading multiple markdown files

2. **Multi-Site Posting**
   - Automatically posts all documents to all configured WordPress sites

3. **Verification Workflow**
   - Ensures all documents are successfully posted to the target sites

4. **Error Resilience**
   - Handles upload, posting, and verification errors gracefully

## Potential Enhancements
- Scheduling posts
- Customizing markdown content
- Adding metadata to posts
- Implementing draft/publish modes

---

**Note from the Developers**: This workflow aims to simplify the process of multi-site markdown publishing, reducing manual effort and potential errors in content distribution.
