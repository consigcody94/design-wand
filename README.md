# Design Wand

A Model Context Protocol (MCP) server for Figma workflow automation. Get file details, list components, export assets, manage styles, and track comments.

## Overview

Design Wand connects your AI assistant to Figma, enabling seamless design system management and asset workflows. Query your design files, export assets at multiple scales, and track design review comments.

### Why Use Design Wand?

**Traditional workflow:**
- Open Figma, navigate to file
- Manually select and export assets one by one
- Copy node IDs from the UI
- Check comments in separate panel

**With Design Wand:**
```
"List all components in my design system file"
"Export the header icons at 2x scale as PNG"
"What comments are unresolved on this file?"
"Get the document structure of my landing page design"
```

## Features

- **File Inspection** - Get file metadata and document structure
- **Component Discovery** - List all components with keys and metadata
- **Asset Export** - Export nodes as PNG, JPG, SVG, or PDF at multiple scales
- **Style Management** - Update style names and descriptions
- **Comment Tracking** - Retrieve and review design comments

## Installation

```bash
# Clone the repository
git clone https://github.com/consigcody94/design-wand.git
cd design-wand

# Install dependencies
npm install

# Build the project
npm run build
```

## Configuration

### Getting Your Figma Token

1. Log in to [Figma](https://www.figma.com/)
2. Click your profile icon → Settings
3. Scroll to "Personal access tokens"
4. Click "Generate new token"
5. Give it a description and copy the token (starts with `figd_`)

### Finding File Keys

The file key is in the Figma URL:

```
https://www.figma.com/file/ABC123DEF456/My-Design-File
                            ^^^^^^^^^^^^
                            This is the file key
```

### Claude Desktop Integration

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "design-wand": {
      "command": "node",
      "args": ["/absolute/path/to/design-wand/dist/index.js"]
    }
  }
}
```

**Config file locations:**
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- Linux: `~/.config/Claude/claude_desktop_config.json`

## Tools Reference

### get_file

Get Figma file details and document structure.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `fileKey` | string | Yes | Figma file key from URL |
| `token` | string | Yes | Figma personal access token |

**Example:**

```json
{
  "fileKey": "ABC123DEF456",
  "token": "figd_your_token_here"
}
```

**Response includes:**
- File name and version
- Last modified timestamp
- Thumbnail URL
- Document structure (pages, frames)
- Component count
- Style count

### list_components

List all components in a Figma file.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `fileKey` | string | Yes | Figma file key |
| `token` | string | Yes | Figma personal access token |

**Example:**

```json
{
  "fileKey": "ABC123DEF456",
  "token": "figd_your_token_here"
}
```

**Response includes:**
- Component key (for referencing)
- Component name
- Description
- Node ID (for export)
- Creation and update timestamps

### export_assets

Export nodes/assets from Figma as images.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `fileKey` | string | Yes | Figma file key |
| `nodeIds` | string[] | Yes | Array of node IDs to export |
| `format` | string | Yes | `png`, `jpg`, `svg`, or `pdf` |
| `scale` | number | No | Scale factor 1-4 (default: 1) |
| `token` | string | Yes | Figma personal access token |

**Example - Export icons as PNG at 2x:**

```json
{
  "fileKey": "ABC123DEF456",
  "nodeIds": ["1:2", "1:3", "1:4"],
  "format": "png",
  "scale": 2,
  "token": "figd_your_token_here"
}
```

**Example - Export as SVG:**

```json
{
  "fileKey": "ABC123DEF456",
  "nodeIds": ["5:10"],
  "format": "svg",
  "token": "figd_your_token_here"
}
```

**Response includes:**
- Format and scale used
- Node count
- Download URLs for each exported node (valid for ~30 days)

### update_styles

Update style definitions in a Figma file.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `fileKey` | string | Yes | Figma file key |
| `styleKey` | string | Yes | Style key to update |
| `name` | string | No | New style name |
| `description` | string | No | New description |
| `token` | string | Yes | Figma personal access token |

**Example:**

```json
{
  "fileKey": "ABC123DEF456",
  "styleKey": "S:abc123def456",
  "name": "Primary/Button/Hover",
  "description": "Primary button hover state - use for interactive elements",
  "token": "figd_your_token_here"
}
```

### get_comments

Get all comments from a Figma file.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `fileKey` | string | Yes | Figma file key |
| `token` | string | Yes | Figma personal access token |

**Example:**

```json
{
  "fileKey": "ABC123DEF456",
  "token": "figd_your_token_here"
}
```

**Response includes:**
- Total comment count
- Resolved vs unresolved counts
- For each comment:
  - Comment ID and message
  - User info (name, avatar)
  - Created timestamp
  - Resolution status
  - Node ID and position (if pinned to element)

## Finding Node IDs

### Method 1: Via Figma UI
1. Select a layer in Figma
2. Right-click → "Copy/Paste" → "Copy link"
3. The URL contains the node ID: `...?node-id=1:2`

### Method 2: Via get_file
Use `get_file` to explore the document tree and find node IDs.

### Method 3: Component Keys
Use `list_components` to get component node IDs directly.

### Node ID Format
- Simple: `"1:2"`, `"23:456"`
- URL-encoded: `"1%3A2"` (colon = `%3A`)

## Workflow Examples

### Design System Audit

1. **Get file overview:**
   ```
   get_file with fileKey: "ABC123", token: "figd_..."
   ```

2. **List all components:**
   ```
   list_components with fileKey: "ABC123", token: "figd_..."
   ```

3. **Export component previews:**
   ```
   export_assets with fileKey: "ABC123", nodeIds: [...], format: "png", scale: 2, token: "figd_..."
   ```

### Asset Export Pipeline

1. **Find components to export:**
   ```
   list_components with fileKey: "ABC123", token: "figd_..."
   ```

2. **Export at multiple scales:**
   ```
   export_assets with format: "png", scale: 1, ...
   export_assets with format: "png", scale: 2, ...
   export_assets with format: "png", scale: 3, ...
   ```

3. **Export as SVG for web:**
   ```
   export_assets with format: "svg", ...
   ```

### Design Review

1. **Check for unresolved comments:**
   ```
   get_comments with fileKey: "ABC123", token: "figd_..."
   ```

2. **Review file changes:**
   ```
   get_file with fileKey: "ABC123", token: "figd_..."
   ```

## API Rate Limits

Figma API has rate limits:

| Plan | Limit |
|------|-------|
| Free | ~30 requests/minute |
| Professional | ~100 requests/minute |
| Organization | Higher limits |

If you hit rate limits:
- Wait 60 seconds before retrying
- Batch node IDs in export requests (up to ~500 per request)
- Cache responses when possible

## Export Format Guide

| Format | Best For | Supports Scale |
|--------|----------|----------------|
| `png` | Raster images, icons | Yes (1-4x) |
| `jpg` | Photos, complex images | Yes (1-4x) |
| `svg` | Vector graphics, icons | No (vector) |
| `pdf` | Documents, print | No |

## Requirements

- Node.js 18 or higher
- Figma account
- Personal access token

## Troubleshooting

### "Figma API error (403)"

1. Verify your token is valid and not expired
2. Ensure you have access to the file
3. Check if the file is in a team you belong to

### "Node not found"

1. Verify the node ID format (should be `"1:2"` not `"1%3A2"`)
2. Ensure the node exists in the current file version
3. Check if the node is in a different page

### Export URLs expired

Export URLs are temporary (~30 days). Re-run the export to get fresh URLs.

### Missing components

1. Components must be published to the file's component library
2. Check if components are in a different file (linked library)

## Security Notes

- Never commit tokens to version control
- Tokens have read/write access to files you can access
- Rotate tokens periodically
- Use separate tokens for different applications

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Author

consigcody94
