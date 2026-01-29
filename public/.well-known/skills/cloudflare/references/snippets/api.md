### HTTP Methods
```javascript
request.method // GET, POST, PUT, DELETE, etc.
```

### Response Constructors
```javascript
// Plain text
new Response("Hello", { status: 200 })

// JSON
Response.json({ key: "value" })

// HTML
new Response("<h1>Hi</h1>", { 
  headers: { "Content-Type": "text/html" }
})

// Redirect
Response.redirect("https://example.com", 301)
```

### Header Operations
```javascript
// Request headers
request.headers.get("X-Header")
request.headers.has("X-Header")
request.headers.set("X-Header", "value")
request.headers.delete("X-Header")

// Response headers (must clone first)
const res = new Response(response.body, response);
res.headers.set("X-Header", "value")
res.headers.append("Set-Cookie", "value")
res.headers.delete("X-Header")
```

### URL Operations
```javascript
const url = new URL(request.url);
url.hostname    // "example.com"
url.pathname    // "/path/to/page"
url.search      // "?query=value"
url.searchParams.get("query") // "value"
```

### Cloudflare Properties
```javascript
// Access Cloudflare-specific data via request.cf
request.cf.country          // "US", "GB", etc.
request.cf.botManagement.score    // 0-99 bot score
request.cf.botManagement.verifiedBot // true/false
request.cf.botManagement.ja4       // JA4 fingerprint

// Zone information
request.cf.zone.name        // zone name
request.cf.zone.id          // zone ID
```

### Environment Variables
```javascript
// Access environment variables
env.MY_VARIABLE    // defined in dashboard or API
env.DB_PASSWORD    // secrets stored securely
```

### API Operations
```bash
# List all snippets
GET /zones/{zone_id}/snippets

# Create/update snippet  
PUT /zones/{zone_id}/snippets/{snippet_name}
Content-Type: multipart/form-data
files: @snippet.js
metadata: {"main_module": "snippet.js"}

# Create snippet rule
PUT /zones/{zone_id}/snippets/snippet_rules
{
  "rules": [{
    "description": "Run on homepage",
    "enabled": true,
    "expression": "(http.request.uri.path eq \"/\")",
    "snippet_name": "my_snippet"
  }]
}

# Delete snippet
DELETE /zones/{zone_id}/snippets/{snippet_name}
```

### Error Handling
```javascript
export default {
  async fetch(request, env, ctx) {
    try {
      // Your snippet logic
      return new Response("Success");
    } catch (error) {
      console.error("Snippet error:", error);
      // Fail open - continue to origin
      return fetch(request);
    }
  }
}
```