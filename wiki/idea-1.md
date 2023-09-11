You can download logs from your S3 bucket and push them into a Google Sheets file using various JavaScript libraries and APIs. Below is a step-by-step guide with example code snippets in JavaScript.

### Prerequisites:

1. AWS SDK for JavaScript (in Node.js)
2. Google Sheets API v4 client libraries
3. Node.js and npm installed

### Steps:

1. **Set up AWS SDK and Google Sheets API**: Install both SDKs by running the following commands in your terminal:

    ```bash
    npm install aws-sdk
    npm install googleapis@39 --save
    ```

2. **Download logs from S3**: Use AWS SDK for JavaScript to download logs from S3 bucket.

    ```javascript
    const AWS = require('aws-sdk');
    const s3 = new AWS.S3();

    const params = {
      Bucket: 'your-bucket-name',
      Key: 'your-log-key'
    };

    s3.getObject(params, function(err, data) {
      if (err) console.error(err, err.stack);
      else     parseAndSendToGoogleSheet(data.Body.toString('utf-8'));
    });
    ```

3. **Parse CSV logs**: Once you get the logs, parse them into a format that can be sent to Google Sheets.

    ```javascript
    const csvParse = require('csv-parse/lib/sync');  // npm install csv-parse

    function parseAndSendToGoogleSheet(csvData) {
      const records = csvParse(csvData, { columns: true });
      // Do something with records, e.g., send to Google Sheets
    }
    ```

4. **Send Data to Google Sheets**: Use the Google Sheets API to insert data.

    Here is a code snippet that shows how to authenticate and write data into a Google Sheet.

    ```javascript
    const {google} = require('googleapis');
    const sheets = google.sheets('v4');

    async function writeToSheet(data) {
      await sheets.spreadsheets.values.append({
        spreadsheetId: 'your-spreadsheet-id',
        range: 'Sheet1', // update this to your sheet's name and the range you want to write to
        valueInputOption: 'USER_ENTERED',
        resource: {
          values: data  // 2D array, each array is a row
        },
        auth: 'your-auth-object'  // this comes from Google Sheets API setup
      });
    }
    ```

5. **Combine all together**:

    ```javascript
    const AWS = require('aws-sdk');
    const s3 = new AWS.S3();
    const csvParse = require('csv-parse/lib/sync');
    const {google} = require('googleapis');
    const sheets = google.sheets('v4');

    const params = {
      Bucket: 'your-bucket-name',
      Key: 'your-log-key'
    };

    s3.getObject(params, function(err, data) {
      if (err) console.error(err, err.stack);
      else     parseAndSendToGoogleSheet(data.Body.toString('utf-8'));
    });

    function parseAndSendToGoogleSheet(csvData) {
      const records = csvParse(csvData, { columns: true });
      // Convert records to 2D array
      const sheetData = records.map(record => Object.values(record));
      writeToSheet(sheetData);
    }

    async function writeToSheet(data) {
      await sheets.spreadsheets.values.append({
        spreadsheetId: 'your-spreadsheet-id',
        range: 'Sheet1',
        valueInputOption: 'USER_ENTERED',
        resource: {
          values: data
        },
        auth: 'your-auth-object'
      });
    }
    ```

Note: For Google Sheets API, you'll need to set up OAuth2 and get the auth object. Please refer to the [Google Sheets API Node.js Quickstart Guide](https://developers.google.com/sheets/api/quickstart/nodejs) for setting up authentication.

Now, when you run your script, it should fetch logs from your S3 bucket and populate them into your Google Sheet.