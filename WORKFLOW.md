# MARKET Workflow Documentation

Detailed technical documentation of the MARKET automation workflow.

## 📋 Table of Contents
1. [Workflow Overview](#workflow-overview)
2. [Trigger Configuration](#trigger-configuration)
3. [Step-by-Step Breakdown](#step-by-step-breakdown)
4. [Data Structures](#data-structures)
5. [Error Handling](#error-handling)
6. [Customization Guide](#customization-guide)

## 🎯 Workflow Overview

**Name**: MARKET  
**Type**: ActivePieces Flow  
**Status**: Published  
**Trigger**: Google Sheets - New Row Added  
**Total Steps**: 11 main steps + nested conditionals  

### High-Level Flow

```
┌─────────────────────────────────────────┐
│ Google Sheets - New Row Added (Trigger) │
└─────────────────┬───────────────────────┘
                  │
         ┌────────▼────────┐
         │ Get Product Row │
         └────────┬────────┘
                  │
         ┌────────▼──────────┐
         │ Validate Input    │
         └────────┬──────────┘
                  │
        ┌─────────┴─────────┐
        │                   │
   Valid? ────────────────  Invalid?
   (YES)   (NO)             │
        │                   │
        │         ┌─────────▼────────┐
        │         │ Log Error Status │
        │         └──────────────────┘
        │
   ┌────▼─────────────┐
   │ Clean & Normalize│
   └────┬─────────────┘
        │
   ┌────▼────────────────┐
   │ Extract Keywords    │
   └────┬────────────────┘
        │
   ┌────▼──────────┐
   │ Build Prompt  │
   └────┬──────────┘
        │
   ┌────▼─────────────┐
   │ Generate Listings│
   └────┬─────────────┘
        │
   ┌────▼──────────────┐
   │ Format & Validate │
   └────┬──────────────┘
        │
   ┌────▼──────────────────┐
   │ Update Google Sheet ✅ │
   └───────────────────────┘
```

## 🔔 Trigger Configuration

### Trigger: Google Sheets - New Row Added

**Type**: PIECE_TRIGGER  
**Piece**: @activepieces/piece-google-sheets  
**Version**: 0.16.4  
**Trigger Name**: googlesheets_new_row_added

**Configuration**:
```json
{
  "pieceName": "@activepieces/piece-google-sheets",
  "triggerName": "googlesheets_new_row_added",
  "pieceVersion": "0.16.4",
  "input": {
    "auth": "{{connections['YOUR_GOOGLE_SHEETS_CONNECTION']}}",
    "sheetId": 0,
    "spreadsheetId": "YOUR_SPREADSHEET_ID",
    "includeTeamDrives": false
  }
}
```

**Trigger Output**:
```json
{
  "row": 2,           // Row number (1-indexed)
  "values": {
    "A": "Product Name",
    "B": "Details",
    "C": "Photo URL"
  }
}
```

## 🔄 Step-by-Step Breakdown

### Step 1: Get Product Row

**Type**: PIECE (Google Sheets)  
**Action**: find_row_by_num  
**Purpose**: Retrieve full row data with headers

**Configuration**:
```json
{
  "pieceName": "@activepieces/piece-google-sheets",
  "actionName": "find_row_by_num",
  "input": {
    "auth": "{{connections['YOUR_GOOGLE_SHEETS_CONNECTION']}}",
    "spreadsheetId": "YOUR_SPREADSHEET_ID",
    "sheetId": 0,
    "headerRow": "1",
    "rowNumber": "{{trigger['output']['row']}}",
    "useHeaderNames": true,
    "includeTeamDrives": false
  }
}
```

**Output**:
```json
{
  "values": {
    "Product Name": "Solar Charger",
    "Details": "Portable waterproof charger",
    "Photo URL": "https://example.com/image.jpg",
    "Status": ""
  }
}
```

---

### Step 2: Validate Input

**Type**: CODE  
**Purpose**: Ensure required fields meet minimum requirements

**Validations**:
- ✅ Product Name exists and is ≥ 3 characters
- ✅ Details exists and is ≥ 15 characters
- ✅ No empty values for required fields

**Input Variables**:
```javascript
const {
  status,           // Current status (usually empty)
  details,          // Product details text
  productName       // Product name
} = inputs;
```

**Output Structure**:
```javascript
{
  valid: true,      // Boolean - validation result
  reason: "",       // Error message if invalid
  productName,      // Cleaned product name
  details           // Cleaned details
}
```

**Code Sample**:
```javascript
const fail = (reason) => ({
  valid: false,
  reason
});

const productName = String(inputs.productName || "").trim();
const details = String(inputs.details || "").trim();

if (!productName) return fail("Missing Product Name");
if (productName.length < 3) return fail("Product Name Too Short");
if (!details) return fail("Missing Details");
if (details.length < 15) return fail("Details Too Short");

return {
  valid: true,
  productName,
  details
};
```

---

### Step 3: Router (Valid/Invalid Branch)

**Type**: ROUTER  
**Purpose**: Conditional routing based on validation result

**Conditions**:
- **Branch 1**: `{{step_2['output']['valid']}} === true` → Process product
- **Branch 2**: Otherwise → Log error

**Execution**: EXECUTE_FIRST_MATCH

---

### Step 4: Clean & Normalize

**Type**: CODE  
**Purpose**: Standardize text formatting

**Transformations**:
1. Remove tabs, newlines, extra spaces
2. Collapse multiple spaces to single
3. Remove duplicate commas
4. Ensure space after commas
5. Remove spaces before punctuation
6. Title case for product name

**Functions**:
```javascript
// Remove whitespace issues
const normalize = (text = "") => {
  return String(text)
    .replace(/[\r\n\t]+/g, " ")        // Remove tabs/newlines
    .replace(/\s+/g, " ")              // Collapse spaces
    .replace(/,+/g, ",")               // Remove dup commas
    .replace(/\s*,\s*/g, ", ")         // Space after comma
    .replace(/\s+([.,!?])/g, "$1")     // Remove space before punctuation
    .trim();
};

// Capitalize each word
const titleCase = (text = "") =>
  text.replace(/\w\S*/g, (word) =>
    word.charAt(0).toUpperCase() + word.slice(1).toLowerCase()
  );
```

**Output Example**:
```javascript
{
  productName: "Solar Phone Charger",  // Was: "solar   phone  charger"
  details: "Portable, waterproof, fast."  // Was: "portable  ,waterproof, fast."
}
```

---

### Step 5: Extract Keywords

**Type**: CODE  
**Purpose**: Generate searchable keywords from product data

**Process**:
1. Add product name as primary keyword
2. Split details by commas (features)
3. Extract individual words from features
4. Filter:
   - Minimum 3 characters
   - Not in stop word list
   - Not numbers
5. Remove duplicates using Set

**Stop Words**:
```javascript
["the", "and", "for", "with", "this", "that",
 "from", "into", "your", "you", "our", "has",
 "have", "are", "was", "were"]
```

**Example**:
```
Input Details: "Waterproof, fast charging, portable design"
Features: ["Waterproof", "fast charging", "portable design"]
Words: ["waterproof", "fast", "charging", "portable", "design"]
Keywords: ["solar phone charger", "waterproof", "fast", "charging", 
           "portable", "design"]
```

---

### Step 6: Build Prompt

**Type**: CODE  
**Purpose**: Create AI prompt for marketplace listings

**Template Structure**:
```javascript
const prompt = `
Generate marketplace listings for:
Product: ${productName}
Details: ${details}
Features: ${features.join(", ")}
Keywords: ${keywords.join(", ")}

Create three separate listings:
1. ETSY: Focus on handmade/unique appeal
2. AMAZON: Focus on value/functionality
3. SHOPIFY: Focus on premium positioning

Each listing should be compelling, keyword-rich, and platform-appropriate.
`;
```

**Customizable Elements**:
- Tone (professional, casual, technical)
- Platform emphasis
- Length (character limits)
- Keywords emphasis
- Call-to-action style

---

### Step 7: Generate Listings

**Type**: PIECE (AI)  
**Purpose**: Use AI to generate marketplace-specific product listings

**Configuration**:
```json
{
  "pieceName": "@activepieces/piece-ai",
  "input": {
    "prompt": "{{step_6['output']['prompt']}}",
    "model": "gpt-3.5-turbo"  // or your configured model
  }
}
```

**Output**:
```
ETSY LISTING:
Beautiful solar phone charger perfect for eco-conscious shoppers...

AMAZON LISTING:
Premium 25000mAh Solar Power Bank with dual USB ports...

SHOPIFY LISTING:
Ultra-portable solar charging solution for modern professionals...
```

---

### Step 8: Format & Validate Output

**Type**: CODE  
**Purpose**: Parse and validate AI-generated listings

**Validations**:
- ✅ Valid JSON format
- ✅ Contains all three listings
- ✅ Each listing meets minimum length
- ✅ No HTML/special formatting issues

**Output Structure**:
```javascript
{
  etsy: "...",        // Etsy listing draft
  amazon: "...",      // Amazon listing draft
  shopify: "...",     // Shopify listing draft
  generatedAt: "2024-08-08T10:30:00Z",
  status: "Success"
}
```

---

### Step 9: Update Google Sheet

**Type**: PIECE (Google Sheets)  
**Action**: update_row  
**Purpose**: Write results back to spreadsheet

**Update Mapping**:
```json
{
  "auth": "{{connections['YOUR_GOOGLE_SHEETS_CONNECTION']}}",
  "spreadsheetId": "YOUR_SPREADSHEET_ID",
  "sheetId": 0,
  "rowNumber": "{{trigger['output']['row']}}",
  "values": {
    "Etsy Draft": "{{step_8['output']['etsy']}}",
    "Amazon Draft": "{{step_8['output']['amazon']}}",
    "Shopify Draft": "{{step_8['output']['shopify']}}",
    "Status": "Processed"
  }
}
```

---

### Error Path: Log Error Status

**Type**: CODE  
**Purpose**: Handle validation failures

**Output**:
```javascript
{
  status: "Error",
  reason: "{{step_2['output']['reason']}}",
  updatedAt: new Date().toISOString()
}
```

**Updates Sheet Column G** with error message.

---

## 📦 Data Structures

### Trigger Output
```typescript
{
  row: number;           // Row number in spreadsheet
  values: {
    [key: string]: string;
  }
}
```

### Validation Output
```typescript
{
  valid: boolean;
  reason?: string;       // Only if invalid
  productName: string;
  details: string;
}
```

### Processed Output
```typescript
{
  productName: string;
  details: string;
  features: string[];
  keywords: string[];
  etsy: string;
  amazon: string;
  shopify: string;
  generatedAt: string;
  status: "Success" | "Error";
}
```

## 🚨 Error Handling

### Error Scenarios

| Scenario | Detection | Handling |
|----------|-----------|----------|
| Missing fields | Validation step | Log error, update Status |
| Short content | Validation step | Log error, update Status |
| Google Sheets timeout | Piece level | Retry (default 3x) |
| AI service error | Piece level | Continue on failure flag |
| Invalid sheet format | Trigger | Flow won't start |

### Retry Configuration
```json
{
  "retryOnFailure": true,
  "retryCount": 3,
  "retryDelay": 1000  // milliseconds
}
```

### Logging Errors
- Error messages stored in Status column
- Execution logs available in ActivePieces dashboard
- Failed products can be manually reprocessed

## 🎨 Customization Guide

### Modify Validation Rules

Edit **Step 2 - Validate Input**:
```javascript
// Change minimum lengths
if (productName.length < 5) { }  // Changed from 3
if (details.length < 20) { }     // Changed from 15

// Add new validations
const email = inputs.email || "";
if (!email.includes("@")) {
  return fail("Invalid email format");
}
```

### Change Keyword Extraction

Edit **Step 5 - Extract Keywords**:
```javascript
// Add more stop words
const stopWords = new Set([...existing, "new", "best", "free"]);

// Adjust minimum word length
if (word.length >= 4) { }  // Changed from 3

// Add keyword filtering
if (keyword.includes("low-quality-keyword")) continue;
```

### Customize AI Prompts

Edit **Step 6 - Build Prompt**:
```javascript
// Add brand voice
const prompt = `
You are writing for ${inputs.brandVoice} audience.
Create ${inputs.tone} marketplace listings...
`;

// Add character limits
const prompt = `
ETSY (max 500 chars): ...
AMAZON (max 1000 chars): ...
`;
```

### Add New Marketplace

1. Duplicate listing generation steps
2. Modify AI prompt for new platform
3. Add new Google Sheet column
4. Update output mapping

```javascript
// In Step 8 output mapping
{
  "newPlatform": "{{step_7['output']['new_platform']}}"
}

// In Step 9 Google Sheets update
{
  "New Platform": "{{step_8['output']['newPlatform']}}"
}
```

### Add Pre-Processing

Insert new CODE step before validation:
```javascript
// Fetch product details from external API
const response = await fetch(`https://api.example.com/product/${id}`);
const data = await response.json();

return {
  ...inputs,
  categoryFromAPI: data.category,
  priceFromAPI: data.price
};
```

---

## 🔍 Debugging Tips

### View Execution Logs
1. Go to Flow → Executions
2. Click on failed run
3. Review inputs/outputs for each step

### Test Individual Steps
1. Use "Test Mode" in ActivePieces
2. Provide sample data
3. Run step-by-step

### Common Issues

**"Spreadsheet not found"**
- Verify spreadsheet ID
- Check Google Sheets authorization
- Ensure spreadsheet is accessible

**"Invalid row number"**
- Confirm row exists before running
- Check if row was deleted
- Validate trigger is firing correctly

**"Listings not generated"**
- Check AI service credentials
- Verify API keys are valid
- Review AI service rate limits

---

## 📚 References

- [ActivePieces Documentation](https://docs.activepieces.com)
- [Google Sheets Integration](https://docs.activepieces.com/pieces/apps/google-sheets)
- [Code Pieces Guide](https://docs.activepieces.com/pieces/pieces-type/code)
- [AI Pieces Guide](https://docs.activepieces.com/pieces/apps/openai)

---

**Last Updated**: 2024-08-08  
**Version**: 1.0  
**Maintained by**: arasu vicky
