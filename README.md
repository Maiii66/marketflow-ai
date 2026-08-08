# MARKET - Marketplace Listing Automation

An **ActivePieces workflow** that automates product listing generation and management across multiple e-commerce platforms.

## 📋 Overview

MARKET is an intelligent automation workflow that:
- **Monitors** a Google Sheet for new product entries
- **Validates** product information (name, details, images)
- **Cleans & Normalizes** data (formatting, capitalization, punctuation)
- **Extracts Keywords** automatically from product details
- **Generates Listings** optimized for Etsy, Amazon, and Shopify
- **Updates** the Google Sheet with generated listings

## 🎯 Features

✅ **Automatic Validation**
- Checks for required fields (Product Name, Details)
- Ensures minimum content length (3+ chars for name, 15+ for details)
- Validates before processing

✅ **Smart Data Processing**
- Removes extra spaces, tabs, newlines
- Normalizes punctuation and spacing
- Title case formatting for product names
- Extracts meaningful keywords (removes stop words)

✅ **Multi-Platform Support**
- Generates listing drafts for Etsy
- Generates listing drafts for Amazon
- Generates listing drafts for Shopify
- Stores results back in Google Sheet

✅ **Error Handling**
- Input validation with detailed error messages
- Conditional routing for valid/invalid entries
- Retry logic for sheet operations

## 🏗️ Architecture

### Workflow Stages

1. **Trigger**: `New Row Added` (Google Sheets)
2. **Get Row Data**: Fetch the new row with headers
3. **Validate Input**: Check required fields and content length
4. **Router**: Branch based on validation results
   - ✅ **Valid Path**:
     - Clean & Normalize text
     - Extract keywords
     - Build AI prompts
     - Generate marketplace listings
     - Format output
     - Update Google Sheet
   - ❌ **Invalid Path**: Store error message

### Key Components

- **Google Sheets Integration**: Trigger + read/write operations
- **Custom Code Blocks**: 5 validation and processing steps
- **AI Integration**: Uses ActivePieces AI piece for listing generation
- **Data Formatting**: Output normalization and JSON validation

## 📊 Data Flow

```
Google Sheet (New Row)
    ↓
Validate Input (Product Name, Details)
    ↓
    ├─ Valid ────→ Clean & Normalize
    │              ↓
    │         Extract Keywords
    │              ↓
    │         Build AI Prompt
    │              ↓
    │         Generate Listings
    │              ↓
    │         Format Output
    │              ↓
    │         Update Google Sheet ✅
    │
    └─ Invalid ──→ Log Error Message ❌
```

## 🔧 Setup Instructions

### Prerequisites
- **Google Account** with Google Sheets access
- **ActivePieces Account** (self-hosted or cloud)
- **AI Service** credentials (for listing generation)

### Installation

1. **Import Workflow**
   - Go to ActivePieces
   - Create new flow or import from JSON
   - Upload `MARKET__7_.json`

2. **Configure Google Sheets**
   - Set up a Google Sheet with columns:
     - A: Product Name
     - B: Details
     - C: Photo URL
     - D: Etsy Draft
     - E: Amazon Draft
     - F: Shopify Draft
     - G: Status

3. **Connect Credentials**
   - Authenticate Google Sheets connection
   - Configure AI service credentials

4. **Test**
   - Add a test product to your sheet
   - Monitor the flow execution
   - Verify listings appear in columns D, E, F

## 📝 Google Sheet Schema

| Column | Field | Type | Required |
|--------|-------|------|----------|
| A | Product Name | Short Text | ✅ |
| B | Details | Short Text | ✅ |
| C | Photo URL | Short Text | ❌ |
| D | Etsy Draft | Short Text | ❌ |
| E | Amazon Draft | Short Text | ❌ |
| F | Shopify Draft | Short Text | ❌ |
| G | Status | Short Text | ❌ |

### Validation Rules
- **Product Name**: Minimum 3 characters
- **Details**: Minimum 15 characters
- Both fields are required

## 📦 Project Metadata

- **Author**: arasu vicky
- **Type**: SHARED
- **Status**: PUBLISHED
- **Last Updated**: 2026-08-08
- **Pieces Used**:
  - `@activepieces/piece-google-sheets` v0.16.4
  - `@activepieces/piece-ai`

## 🚀 Usage Example

### Input (Google Sheet)
```
Product Name: "Solar Phone Charger"
Details: "Portable, waterproof, 25000mAh capacity, dual USB ports"
Photo URL: "https://example.com/charger.jpg"
```

### Output (Generated)
```
Status: "Processed"
Etsy Draft: "Eco-friendly solar phone charger - portable & waterproof..."
Amazon Draft: "25000mAh Solar Power Bank - Dual USB Fast Charging..."
Shopify Draft: "Premium Solar Phone Charger | 25000mAh Capacity..."
```

## 🔍 What It Extracts

**Keywords** (from product details):
- Removes stop words (the, and, for, with, etc.)
- Extracts meaningful terms (3+ characters)
- Removes numbers and special characters
- Creates searchable keyword set

**Features** (from comma-separated details):
- Splits details by commas
- Each feature becomes searchable metadata

## 🎓 Learning Resources

- [ActivePieces Documentation](https://docs.activepieces.com)
- [Google Sheets Integration](https://docs.activepieces.com/pieces/apps/google-sheets)
- [Code Pieces Guide](https://docs.activepieces.com/pieces/pieces-type/code)

## 📄 License

This workflow is shared under the ActivePieces community. Feel free to fork, modify, and adapt for your use case.

## 💡 Future Enhancements

- [ ] Add inventory tracking
- [ ] Support for image uploads
- [ ] A/B testing for different listing variants
- [ ] Pricing optimization
- [ ] Customer review integration
- [ ] Multi-language support

## 🤝 Contributing

Found a bug or have an improvement? Open an issue or submit a PR!

## 📞 Support

For issues or questions:
- Check [ActivePieces Community](https://community.activepieces.com)
- Review error logs in your flow execution
- Verify Google Sheets credentials

---

**Built with ❤️ using ActivePieces**
