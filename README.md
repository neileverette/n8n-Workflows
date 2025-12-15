# n8n Document Extraction Workflow

## Overview
This n8n workflow automatically extracts content from uploaded documents (PDFs, etc.), uses AI to identify and extract key information, and appends the results to a Google Sheet.

## Workflow Components

### 1. **Webhook - Document Upload**
- Receives document uploads via POST request
- Endpoint: `/document-upload`
- Accepts binary file data

### 2. **Extract Binary File**
- Converts the uploaded file data to binary format
- Handles various document types (PDF, DOCX, etc.)

### 3. **Extract PDF Text**
- Extracts text content from the document
- Uses n8n's built-in file extraction capabilities

### 4. **AI - Extract Key Information**
- Uses Google Gemini AI to analyze document content
- Extracts structured information:
  - Document Title
  - Date
  - Author/Contact Name
  - Summary
  - Key Topics
  - Action Items

### 5. **Format Data for Sheets**
- Parses AI response into structured JSON
- Handles errors gracefully
- Adds timestamp for tracking

### 6. **Append to Google Sheets**
- Appends extracted data to your Google Sheet
- Creates a new row for each processed document

## Setup Instructions

### Prerequisites
1. n8n instance (self-hosted or cloud)
2. Google Gemini API key
3. Google Sheets API access

### Configuration Steps

1. **Import the Workflow**
   - In n8n, go to Workflows → Import from File
   - Select `Document-extract-upload-to-Sheets-Gemini.json`

2. **Configure Google Gemini Credentials**
   - Go to Credentials → Add Credential
   - Select "Google Gemini API"
   - Enter your Gemini API key
   - Update the credential ID in the "AI - Extract Key Information" node

3. **Configure Google Sheets Credentials**
   - Go to Credentials → Add Credential
   - Select "Google Sheets OAuth2 API"
   - Authenticate with your Google account
   - Update the credential ID in the "Append to Google Sheets" node

4. **Set Your Google Sheet ID**
   - Open your target Google Sheet
   - Copy the spreadsheet ID from the URL:
     `https://docs.google.com/spreadsheets/d/YOUR_SPREADSHEET_ID/edit`
   - Update the "documentId" value in the "Append to Google Sheets" node

5. **Create Sheet Headers**
   - In your Google Sheet, add these column headers:
     - Title
     - Date
     - Author
     - Summary
     - Topics
     - Action Items
     - Processed

6. **Activate the Workflow**
   - Click "Active" toggle in n8n

## Usage

### Upload a Document

Send a POST request to your webhook URL:

```bash
curl -X POST \
  https://your-n8n-instance.com/webhook/document-upload \
  -H 'Content-Type: application/json' \
  -d '{
    "body": {
      "filename": "report.pdf",
      "mimeType": "application/pdf",
      "data": "base64_encoded_file_content"
    }
  }'
```

Or use the n8n webhook test feature to upload documents manually.

## Extracted Fields

The workflow extracts and stores:
- **Title**: Document title or main heading
- **Date**: Any date mentioned in the document
- **Author**: Author or contact name
- **Summary**: Brief 1-2 sentence summary
- **Topics**: Comma-separated key topics
- **Action Items**: Tasks or action items mentioned
- **Processed**: Timestamp when the document was processed

## Customization

### Modify Extracted Fields
Edit the prompt in the "AI - Extract Key Information" node to extract different fields.

### Change AI Model
Update the `modelId` parameter to use different Gemini models:
- `gemini-1.5-pro` (default, most capable)
- `gemini-1.5-flash` (faster, more economical)

### Adjust AI Temperature
Lower temperature (0.1-0.3) for more consistent extraction.
Higher temperature (0.7-1.0) for more creative summaries.

## Troubleshooting

### Documents Not Uploading
- Check webhook URL is correct
- Verify file encoding (should be base64 for binary files)
- Check n8n logs for errors

### AI Extraction Fails
- Verify Gemini API credentials
- Check API quota limits
- Review document text extraction quality

### Google Sheets Not Updating
- Verify OAuth credentials are valid
- Check spreadsheet ID is correct
- Ensure sheet name matches configuration
- Verify column headers exist

## Advanced Features

### Add Error Notifications
Add an "Send Email" or "Slack" node after error outputs to get notified of failures.

### Batch Processing
Modify the workflow to process multiple documents in a loop.

### Store Original Documents
Add a Google Drive or S3 node to archive uploaded documents.

## License
MIT License - Feel free to modify and use as needed.
