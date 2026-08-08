# Quick Start Guide

Get MARKET up and running in 5 minutes!

## Step 1: Prepare Your Google Sheet

1. Open [Google Sheets](https://sheets.google.com)
2. Create a new spreadsheet named "Product Listings"
3. Add these column headers in Row 1:
   - A1: `Product Name`
   - B1: `Details`
   - C1: `Photo URL`
   - D1: `Etsy Draft`
   - E1: `Amazon Draft`
   - F1: `Shopify Draft`
   - G1: `Status`

4. Copy the **Spreadsheet ID** from the URL:
   ```
   https://docs.google.com/spreadsheets/d/[SPREADSHEET_ID]/edit
   ```

## Step 2: Access ActivePieces

1. Go to [ActivePieces](https://activepieces.com) or your self-hosted instance
2. Log in to your account
3. Go to **Flows** and click **Create New Flow**

## Step 3: Import the Workflow

### Option A: Import JSON File
1. Click **Import** (if available)
2. Select `MARKET__7_.json`
3. Confirm the import

### Option B: Manual Setup
1. Create a new flow called "MARKET"
2. Add trigger: **Google Sheets** → **New Row Added**
3. Copy each code block from the JSON into your flow

## Step 4: Configure Connections

### Google Sheets Connection
1. Click on the trigger **"New Row Added"**
2. Click **Connect** next to Google Sheets
3. Authorize your Google account
4. Select your spreadsheet
5. Set **Sheet** to your product sheet

### Configure Each Step
1. **Get Row Data** step:
   - Spreadsheet ID: Paste the ID from Step 1
   - Sheet ID: 0 (first sheet)
   - Header Row: 1

2. **Generate Listings** step:
   - Ensure AI credentials are configured
   - Select your preferred AI model

## Step 5: Enable & Test

1. Click **Enable Flow** (toggle to ON)
2. Go to your Google Sheet
3. Add a test product:
   ```
   Product Name: "Test Product"
   Details: "This is a comprehensive test product description with details"
   ```
4. Watch the automation run!
5. Check columns D, E, F for generated listings

## 🎯 Sample Test Data

Copy this into your sheet to test:

| Product Name | Details |
|---|---|
| Wireless Charger | Fast charging compatible with all devices. Sleek design. Non-slip surface. LED indicator. |
| Travel Backpack | Spacious 30L capacity with multiple compartments. Waterproof material. Comfortable straps. |
| Desk Lamp | Adjustable brightness. USB powered. Warm white light. Reduces eye strain. |

## ⚙️ Configuration Reference

### Validation Rules
- Product Name: **minimum 3 characters**
- Details: **minimum 15 characters**
- Both fields required to proceed

### Stopping Errors
If flow stops, check:
1. ✅ Google Sheets is authorized
2. ✅ Spreadsheet ID is correct
3. ✅ Column headers match exactly
4. ✅ Product Name and Details aren't empty
5. ✅ AI service has available credits

## 🔧 Troubleshooting

### "Invalid Spreadsheet ID"
- Copy the ID again from the URL
- Remove any extra spaces

### "Sheet not found"
- Verify the sheet number (usually 0 for first sheet)
- Check column headers match exactly

### "Missing Google Sheets Connection"
- Click Connect on the trigger
- Authorize your Google account
- Grant all requested permissions

### Listings Not Generated
- Check AI service credentials
- Verify you have available API credits
- Review the flow logs for specific errors

## 📊 Monitoring

### Check Flow Execution
1. Open your flow in ActivePieces
2. Click **Executions** tab
3. View recent runs
4. Click a run to see:
   - Input data
   - Output results
   - Errors (if any)

### View Generated Content
1. Open your Google Sheet
2. Check columns D, E, F for drafts
3. Column G shows status (Processed/Error)

## 🚀 Advanced Configuration

### Custom AI Prompts
Edit the **"Build Prompt"** code step to customize:
- Listing tone (formal, casual, technical)
- Feature emphasis
- SEO keyword density
- Character limits

### Add More Platforms
1. Duplicate the marketplace listing steps
2. Modify the AI prompt for new platform
3. Add new columns to your sheet
4. Update the output mapping

### Enable Error Notifications
1. Add email or Slack notification step
2. Place after validation step
3. Send alerts when validation fails

## 📚 Next Steps

- Review the [main README](README.md) for full documentation
- Check [ActivePieces Docs](https://docs.activepieces.com) for advanced features
- Customize prompts for your brand voice
- Scale to multiple product sheets

---

**Questions?** Check the troubleshooting section above or visit the ActivePieces community forums.
