# Design Wand

A production-ready Model Context Protocol (MCP) server for Figma workflow automation.

## Features

- **get_file** - Get Figma file details and document structure
- **list_components** - List all components in a file
- **export_assets** - Export nodes/assets as images (PNG, JPG, SVG, PDF)
- **update_styles** - Update style definitions
- **get_comments** - Retrieve all comments from a file

## Installation

```bash
npm install
npm run build
```

## Configuration

### Figma Setup

1. Log in to [Figma](https://www.figma.com/)
2. Go to Settings → Account → Personal Access Tokens
3. Generate a new personal access token
4. Copy the token (starts with `figd_...`)

### File Key

The file key is found in the Figma URL:
```
https://www.figma.com/file/ABC123DEF456/My-Design
                            ^^^^^^^^^^^^
                            This is the file key
```

## Usage

### Claude Desktop Configuration

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "design-wand": {
      "command": "node",
      "args": ["/path/to/design-wand/dist/index.js"]
    }
  }
}
```

### Tool Examples

#### Get File Details

```javascript
{
  "fileKey": "ABC123DEF456",
  "token": "figd_..."
}
```

Returns:
- File name and metadata
- Last modified timestamp
- Thumbnail URL
- Document structure overview
- Component and style counts

#### List Components

```javascript
{
  "fileKey": "ABC123DEF456",
  "token": "figd_..."
}
```

Returns all components with:
- Component key and name
- Description
- Node ID
- Creation and update timestamps

#### Export Assets

```javascript
{
  "fileKey": "ABC123DEF456",
  "nodeIds": ["1:2", "1:3"],
  "format": "png",
  "scale": 2,
  "token": "figd_..."
}
```

Supported formats:
- `png` - PNG image (supports scale)
- `jpg` - JPEG image (supports scale)
- `svg` - SVG vector (scalable by default)
- `pdf` - PDF document

Scale options: 1, 2, 3, 4 (default: 1)

Returns download URLs for each exported node.

#### Update Styles

```javascript
{
  "fileKey": "ABC123DEF456",
  "styleKey": "S:abc123...",
  "name": "Primary/Button",
  "description": "Primary button style",
  "token": "figd_..."
}
```

Update style properties:
- `name` - Style name (optional)
- `description` - Style description (optional)

#### Get Comments

```javascript
{
  "fileKey": "ABC123DEF456",
  "token": "figd_..."
}
```

Returns all comments with:
- Comment ID and message
- User information
- Created and resolved timestamps
- Node ID and position
- Resolution status

## Finding Node IDs

To find node IDs in Figma:

1. Select a layer/object in Figma
2. Right-click → Copy/Paste → Copy as PNG
3. Or use the Figma API to explore the document tree
4. Node IDs are in the format `"1:2"`, `"23:456"`, etc.

## API Rate Limits

Figma API has rate limits:
- Standard: 100 requests per minute
- With OAuth: Higher limits available

## Development

```bash
# Install dependencies
npm install

# Build
npm run build

# Run
npm start
```

## Requirements

- Node.js 18+
- Figma account
- Personal access token

## Common Use Cases

1. **Design System Management**
   - List and audit components
   - Export component assets
   - Update style definitions

2. **Asset Export**
   - Batch export icons and images
   - Generate multiple scales (1x, 2x, 3x)
   - Export in various formats

3. **Design Review**
   - Fetch and track comments
   - Monitor file changes
   - Review design metadata

4. **Automation**
   - Automated asset generation
   - Design token extraction
   - Component inventory

## Security Notes

- Never commit tokens to version control
- Use environment variables or secure vaults
- Rotate tokens regularly
- Limit token scope when possible

## License

MIT

## Author

consigcody94
