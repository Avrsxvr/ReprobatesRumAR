# Google Sheets Email Capture System Setup

Since this is a static website (HTML/JS), we need a backend to handle the email submission and save it to Google Sheets. We will use a **Google Apps Script** for this, which is free and works perfectly.

## Step 1: Create the Google Sheet
1. Go to [Google Sheets](https://sheets.google.com) and create a new sheet.
2. Name it `DonaldJones Emails` (or whatever you prefer).
3. By default, the first column (Column A) will be used for Emails and the second (Column B) for Timestamp.

## Step 2: Create the Script
1. In your Google Sheet, click on `Extensions` > `Apps Script`.
2. Delete any code in the `Code.gs` file and paste the following code:

```javascript
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    var data = JSON.parse(e.postData.contents);
    var email = data.email;
    
    // Validate email simple check
    if (!email || !email.includes('@')) {
      return ContentService.createTextOutput("No");
    }

    // Append email and timestamp
    sheet.appendRow([email, new Date()]);
    
    // Return "Yes" for success
    return ContentService.createTextOutput("Yes");
    
  } catch (error) {
    // Return "No" for error
    return ContentService.createTextOutput("No");
  }
}

// Handle OPTIONS request for CORS (Cross-Origin Resource Sharing)
function doOptions(e) {
  return ContentService.createTextOutput("").setMimeType(ContentService.MimeType.TEXT);
}
```

## Step 3: Deploy the Script
1. Click the blue `Deploy` button > `New deployment`.
2. Click the specific `Select type` gear icon > `Web app`.
3. Fill in the details:
   - **Description**: Email Saver
   - **Execute as**: `Me` (your email)
   - **Who has access**: `Anyone` (This is CRITICAL so the website can access it)
4. Click `Deploy`.
5. You might be asked to `Authorize access`. Click `Review permissions`, choose your account, click `Advanced` > `Go to Untitled project (unsafe)` (it is safe, it's your own code) > `Allow`.
6. **Copy the Web app URL**. It will look like `https://script.google.com/macros/s/.../exec`.

## Step 4: Update Your Website
1. Open `index.html` in your editor.
2. Look for the `const SCRIPT_URL = '...';` line (I will add this for you now).
3. Paste your Web app URL inside the quotes.

That's it! When a user submits an email, it will save to your sheet and the system will alert "Yes".
