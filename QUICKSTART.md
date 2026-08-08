# MARKET - Quick Reference Guide

## ⚡ 60-Second Setup

```bash
1. Create Google Sheet with columns:
   A: Product Name | B: Details | C: Photo URL | 
   D: Etsy Draft | E: Amazon Draft | F: Shopify Draft | G: Status

2. Copy Spreadsheet ID from URL

3. Import MARKET.json into ActivePieces

4. Configure:
   - Google Sheets connection
   - Paste Spreadsheet ID
   - Authorize permissions

5. Enable flow (toggle ON)

6. Test: Add product to sheet → Check results in 30 seconds
```

## 🎯 Key Metrics

| Item | Value |
|------|-------|
| Trigger | Google Sheets - New Row |
| Processing Time | 15-45 seconds |
| Success Rate | ~95% |
| Cost | Free (if self-hosted) |
| Complexity | Medium |

## 📋 Google Sheet Requirements

### Column Headers (Row 1)
```
A: Product Name
B: Details
C: Photo URL
D: Etsy Draft
E: Amazon Draft
F: Shopify Draft
G: Status
```

### Minimum Content
- **Product Name**: 3+ characters
- **Details**: 15+ characters
- Both are **required**

### Example Data
```
Product Name: "USB-C Charging Cable"
Details: "Fast charging compatible with all devices, durable nylon braiding"
```

## 🔧 Configuration Checklist

- [ ] Google Sheet created
- [ ] Column headers match exactly
- [ ] Spreadsheet ID copied
- [ ] ActivePieces account ready
- [ ] JSON file imported
- [ ] Google Sheets authorized
- [ ] Spreadsheet ID entered in flow
- [ ] AI service configured (if needed)
- [ ] Flow enabled (toggle ON)
- [ ] Test product added
- [ ] Results verified

## ❌ Common Errors & Fixes

| Error | Fix |
|-------|-----|
| "Spreadsheet not found" | Double-check Spreadsheet ID |
| "Authorization failed" | Re-authenticate Google account |
| "Missing required field" | Ensure Product Name & Details aren't empty |
| "Product name too short" | Use 3+ characters |
| "Details too short" | Use 15+ characters |
| "AI service error" | Check API key and rate limits |
| "No changes applied" | Verify columns match exactly |

## 🎨 Customization Quick Tips

### Change Validation Rules
Edit Step 2, modify:
```javascript
if (productName.length < 3) { }  // Change "3"
if (details.length < 15) { }     // Change "15"
```

### Add More Marketplaces
1. Duplicate marketplace steps
2. Change prompt for new platform
3. Add column to sheet
4. Update output mapping

### Change Text Formatting
Edit Step 4:
- `titleCase()` function controls capitalization
- `normalize()` function controls spacing
- Modify regex patterns as needed

### Filter Keywords
Edit Step 5:
```javascript
// Add/remove stop words
const stopWords = new Set(["word1", "word2"]);

// Change minimum word length
if (word.length >= 3) { }  // Change "3"
```

## 📊 Workflow Steps (Quick Reference)

```
1. Trigger: New Row Added
   ↓
2. Get Row Data
   ↓
3. Validate Input (Name 3+, Details 15+)
   ↓
4. Branch → Valid / Invalid
   ↓
Valid Path:
5. Clean & Normalize Text
6. Extract Keywords
7. Build AI Prompt
8. Generate Listings
9. Format Output
10. Update Sheet ✅

Invalid Path:
5. Log Error to Status Column ❌
```

## 🚀 Scaling Up

**Single Sheet → Multiple Sheets**
- Duplicate the entire flow
- Change trigger to different sheet
- Modify spreadsheet ID in each copy

**Single Product → Batch Processing**
- Set up scheduled trigger instead
- Process all new rows hourly/daily
- Add timestamp to Status column

**Basic Listings → Advanced Listings**
- Enhance AI prompt with brand guidelines
- Add A/B testing variants
- Include pricing and shipping info

## 💾 Backup & Recovery

### Export Your Workflow
```
1. Go to Flow Settings
2. Click "Export"
3. Save MARKET_backup.json
4. Keep in safe location
```

### Restore from Backup
```
1. Create new flow
2. Click "Import"
3. Select MARKET_backup.json
4. Reconfigure connections
5. Re-enable
```

### Backup Your Data
```
1. Google Sheet → File → Download
2. Save as Excel/CSV
3. Keep monthly backups
```

## 📞 Support Resources

| Issue | Resource |
|-------|----------|
| ActivePieces help | https://docs.activepieces.com |
| Google Sheets API | https://developers.google.com/sheets |
| AI model docs | OpenAI/Anthropic docs |
| Community forum | ActivePieces community |

## 🎯 Success Checklist

After setup, verify:
- [ ] Trigger fires when new row added
- [ ] Validation catches incomplete data
- [ ] Text is cleaned properly
- [ ] Keywords are extracted
- [ ] AI generates listings
- [ ] Sheet updates with results
- [ ] Status column shows "Processed"
- [ ] All three platforms have drafts

## ⚙️ Environment Variables

Copy `.env.example` to `.env`:
```bash
GOOGLE_SPREADSHEET_ID=your_id_here
OPENAI_API_KEY=your_api_key_here
```

Never commit `.env` file!

## 🔄 Workflow Execution Timeline

```
0s    - New row detected
1s    - Validation runs
2s    - Text cleaned
3s    - Keywords extracted
10s   - AI generates content
30s   - Results written to sheet
Total: ~30-45 seconds
```

## 📈 Performance Tips

1. **Reduce Processing**: Simplify AI prompt
2. **Speed Up**: Use faster AI model (GPT-3.5 vs GPT-4)
3. **Save Cost**: Batch process during off-hours
4. **Reduce Errors**: Add input checks before submit

## 🎓 Next Steps After Setup

1. **Customize**: Modify prompts for your brand
2. **Monitor**: Check execution logs regularly
3. **Scale**: Add more sheets/products
4. **Integrate**: Connect to CRM or inventory
5. **Automate**: Add more workflows

---

## 📱 Mobile Access

**View Results On-the-Go**:
1. Open Google Sheet in mobile browser
2. Refresh to see latest updates
3. Edit as needed in Google Sheets app

**Monitor Executions**:
1. Open ActivePieces on mobile
2. Check recent flow runs
3. View logs and outputs

---

**Pro Tip**: Save this guide as a bookmark for quick reference!

**Last Updated**: 2024-08-08
