# Mass Update Feature Documentation

## Overview
The enhanced MLS vs CAMA Comparison Tool now includes automated generation of CAMA system mass update files.

## New Features

### 🔄 CAMA Mass Updates Section
Located at the bottom of the comparison results page, this section automatically generates two mass update files:

1. **SALETAB_MassUpdate** - Excel file for updating the SALE tab in CAMA
2. **MassEntrance** - CSV file for updating the ENTRANCE in CAMA

## How It Works

### Step 1: Run Comparison
Upload your MLS and CAMA data files and run the comparison as usual. The app will generate:
- Perfect Matches
- Value Mismatches
- Missing in CAMA
- Missing in MLS

### Step 2: Configure User Information
In the "🔄 CAMA Mass Updates" section (found after download buttons):

1. **Enter Your Initials** (e.g., "JMJ")
   - Used for the USER1 field in SALETAB_MassUpdate
   
2. **Enter Your Full Name** (e.g., "Jason Jeffries")
   - Used for appraiser and Last Changed By fields in MassEntrance

### Step 3: Generate Files
Click the "🔄 Generate Mass Update Files" button. The app will:
- Combine Perfect Matches and Value Mismatches
- Extract the first SALEKEY from multi-parcel sales
- Extract the first Listing Number
- Generate both files with proper formatting

### Step 4: Download
Two download buttons will appear:
- 📥 Download SALETAB_MassUpdate (Excel)
- 📥 Download MassEntrance (CSV)

Files are automatically named with today's date in the format you need:
- `SALETAB_MassUpdate_MMDDYY.xlsx`
- `MassEntranceMMDDYY.csv`

## File Specifications

### SALETAB_MassUpdate.xlsx
**Columns:**
- `PARID` - Parcel ID (integer)
- `SALEKEY` - First SALEKEY from the sale (integer)
- `USER11` - First Listing Number (integer)
- `SOURCE` - Always 0 (integer)
- `SALEVAL` - Always 0 (integer)
- `USER1` - Your initials (text)
- `USER2` - Current date in YYYY-MM-DD format (date)

**Notes:**
- If a sale has multiple SALEKEYs (e.g., "285317, 285318"), only the first is used
- Records with missing SALEKEYs are automatically excluded

### MassEntranceMMDDYY.csv
**Columns:**
- `Change Type` - Always "existing"
- `appraiser` - Your full name
- `parcelnum` - Parcel ID (integer)
- `comment` - Empty
- `Review Status` - Always "Reviewed"
- `Determination` - Empty
- `Est. Value Change` - Empty
- `Last Changed Date/Time` - Current timestamp (MM/DD/YYYY HH:MM)
- `Last Changed By` - Your full name

## Example Workflow

1. Upload `MLS_data.xlsx` and `CAMA_data.xlsx`
2. Enter WindowId and run comparison
3. Review results (e.g., 20 perfect matches, 30 value mismatches)
4. Scroll to "🔄 CAMA Mass Updates" section
5. Enter:
   - Initials: "JMJ"
   - Full Name: "Jason Jeffries"
6. Click "🔄 Generate Mass Update Files"
7. Download both files (50 total records = 20 + 30)
8. Upload files to CAMA system for mass update

## Data Processing Details

### Combining Records
The tool combines ALL records from:
- Perfect Matches (parcels where all compared fields match)
- Value Mismatches (parcels where at least one field doesn't match)

This ensures every parcel that was successfully matched between MLS and CAMA is included in the mass update files.

### Handling Multiple SALEKEYs
When NOPAR > 1 (multiple parcels in one sale), the SALEKEY field contains comma-separated values like "285317, 285318, 285319".

The tool automatically:
1. Extracts the first SALEKEY (285317)
2. Removes trailing commas
3. Converts to integer format

### Handling Listing Numbers
Similar to SALEKEYs, Listing Numbers are extracted from comma-separated lists and the first value is used.

## Deployment Notes

### Required Updates
1. Replace your existing `streamlit_app.py` with `streamlit_app_FIXED_with_mass_updates.py`
2. Ensure your `requirements.txt` includes all dependencies
3. Push to GitHub and redeploy on Streamlit Cloud

### Testing Checklist
- [ ] Upload test MLS and CAMA files
- [ ] Run comparison successfully
- [ ] Enter user information
- [ ] Generate mass update files
- [ ] Verify SALETAB has correct SALEKEY extraction
- [ ] Verify MassEntrance has correct timestamp format
- [ ] Download both files
- [ ] Verify file formats match CAMA system requirements

## Troubleshooting

**Problem:** "No data available for mass updates"
- **Solution:** Run the comparison first to generate perfect matches and value mismatches

**Problem:** SALETAB has fewer records than expected
- **Solution:** Some records may have missing SALEKEYs and are automatically excluded

**Problem:** Timestamp format doesn't match CAMA requirements
- **Solution:** Check if CAMA expects a different date/time format and update the code

## Future Enhancements

Potential improvements for future versions:
- Option to select which records to include (perfect matches only, mismatches only, or both)
- Custom date format selection
- Preview of generated data before download
- Validation warnings for missing required fields
- Option to exclude specific parcels
