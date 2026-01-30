## Configuration Methods

### 1. Dashboard (GUI)
```
1. Go to zone → Rules → Snippets
2. Create Snippet or select template
3. Enter snippet name (a-z, 0-9, _ only, cannot change later)
4. Write JavaScript code (32KB max)
5. Configure snippet rule:
   - Expression Builder or Expression Editor
   - Use Ruleset Engine filter expressions
6. Deploy or Save as Draft
```

### 2. API
```bash
# Create/update snippet
curl "https://api.cloudflare.com/client/v4/zones/$ZONE_ID/snippets/$SNIPPET_NAME" \
  --request PUT \
  --header "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  --form "files=@example.js" \
  --form "metadata={\"main_module\": \"example.js\"}"

# Create snippet rule
curl "https://api.cloudflare.com/client/v4/zones/$ZONE_ID/snippets/snippet_rules" \
  --request PUT \
  --header "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  --json '{
    "rules": [
      {
        "description": "Trigger snippet on specific cookie",
        "enabled": true,
        "expression": "http.cookie eq \"session=active\"",
        "snippet_name": "my_snippet"
      }
    ]
  }'
```

### 3. Terraform
```hcl
# Create snippet
resource "cloudflare_snippet" "example" {
  zone_id      = var.zone_id
  name         = "example_snippet"
  main_module  = "example.js"
  files {
    name    = "example.js"
    content = file("${path.module}/example.js")
  }
}

# Create snippet rule  
resource "cloudflare_snippet_rules" "example" {
  zone_id = var.zone_id
  rules {
    enabled     = true
    expression  = "(http.request.uri.path eq \"/api\")"
    snippet_name = cloudflare_snippet.example.name
    description = "Run snippet on API calls"
  }
}
```

## Expression Examples

### URL-based
```
# Homepage only
(http.request.uri.path eq "/")

# API endpoints
(starts_with(http.request.uri.path, "/api/"))

# Specific file extensions  
(ends_with(http.request.uri.path, ".jpg"))
```

### Geographic
```
# Specific countries
(ip.geoip.country in {"US" "CA" "MX"})

# Exclude regions
(not ip.geoip.continent eq "EU")
```

### Request attributes
```  
# Method-based
(http.request.method eq "POST")

# Header-based
(http.request.headers["user-agent"][0] contains "mobile")

# Cookie-based
(http.cookie eq "theme=dark") 