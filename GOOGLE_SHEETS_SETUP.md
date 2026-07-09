# Google Sheets Submission Setup

To save form submissions from `ai-seminar-certificate.html` into Google Sheets, follow these steps.

## 1. Create a Google Sheet

1. Open Google Sheets.
2. Create a new sheet and name the first tab `Sheet1` (or update the script to use your tab name).
3. Add the header row in row 1 exactly like this:

   timestamp, formName, firstName, lastName, dob, mobile, email, rating, feedback

## 2. Open Apps Script

1. In the Google Sheet, go to `Extensions` → `Apps Script`.
2. Replace any existing code in `Code.gs` with the script below:

```javascript
function doGet(e) {
  return ContentService
    .createTextOutput(JSON.stringify({status: 'ok', message: 'Sheet endpoint is available.'}))
    .setMimeType(ContentService.MimeType.JSON)
    .setHeader('Access-Control-Allow-Origin', '*');
}

function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Sheet1');
    if (!sheet) {
      throw new Error('Sheet1 not found');
    }

    const values = [
      new Date(),
      e.parameter.formName || '',
      e.parameter.firstName || '',
      e.parameter.lastName || '',
      e.parameter.dob || '',
      e.parameter.mobile || '',
      e.parameter.email || '',
      e.parameter.rating || '',
      e.parameter.feedback || '',
    ];

    sheet.appendRow(values);

    return ContentService
      .createTextOutput(JSON.stringify({status: 'success'}))
      .setMimeType(ContentService.MimeType.JSON)
      .setHeader('Access-Control-Allow-Origin', '*');
  } catch (error) {
    return ContentService
      .createTextOutput(JSON.stringify({status: 'error', message: error.message}))
      .setMimeType(ContentService.MimeType.JSON)
      .setHeader('Access-Control-Allow-Origin', '*');
  }
}
```

## 3. Deploy as a web app

1. Click `Deploy` → `New deployment`.
2. For `Select type`, choose `Web app`.
3. Set `Description` to something like `SMCO certificate form endpoint`.
4. For `Execute as`, select `Me`.
5. For `Who has access`, choose `Anyone` or `Anyone with the link`.
6. Click `Deploy`.
7. Copy the `Web app` URL from the deployment dialog.

## 4. Update the HTML page

1. Open `ai-seminar-certificate.html`.
2. Replace the script URL in the `GOOGLE_SCRIPT_URL_CERTIFICATE` constant with your deployed URL:

```js
const GOOGLE_SCRIPT_URL_CERTIFICATE = 'https://script.google.com/macros/s/your-deployment-id/exec';
```

## 5. Test the form

1. Open `ai-seminar-certificate.html` in a browser.
2. Fill out the form and submit.
3. Verify that the new row appears in the Google Sheet.

## Notes

- The current form sends data via `POST` and uses `application/x-www-form-urlencoded`.
- If the form still shows `Success!`, the submission is likely sent; check the sheet to confirm.
- If you want the sheet to use a different tab name, change `getSheetByName('Sheet1')` in the script.
