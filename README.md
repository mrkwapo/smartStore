I will be building a simplified version of the tools needed to create a smart store, such as , the Marathon Clothing Store by Nipsey Hustle.

This project is ideal for helping a small business owner of a retail store inexpensively transition into a smart store.

The following capabilities:

1. Use Google Sheets to sync inventory data to store website
2. Create Barcodes or QR Codes for merchandise
3. Log and Track inventory on the google sheets using their mobile phone as a scanner
4. Program QR codes to display or run data to inform users
 
****************************************************************************************************
Phase one: Use Google sheets to sync inventory data to store website

https://developers.google.com/sheets/api/quickstart/nodejs

Step 1: Turn on the Google Sheets API

Click this button to create a new Cloud Platform project and automatically enable the Google Sheets API:

ENABLE THE GOOGLE SHEETS API

In resulting dialog click DOWNLOAD CLIENT CONFIGURATION and save the file credentials.json to your working directory.

-When you click “ENABLE THE GOOGLE SHEETS API”,

It will say:

You're all set!

You're ready to start developing!

DOWNLOAD CLIENT CONFIGURATION

Client ID
yourClientIDWillBeHere.apps.googleusercontent.com


Client Secret
yourClientSecretWillBeHere


(You can always manage your API credentials and usage later in the API Console.)


-Click on “DOWNLOAD CLIENT CONFIGURATION” to download the file credentials.json and upload to working directory

Use this website to beautify the json code: https://jsonformatter.curiousconcept.com/
Copy and paste beautiful json in credentials file and save file

***************************************************************************************************

Step 2: Install the client library

Run the following commands to install the libraries using npm:

Copy and paste this in the c9 terminal
npm install googleapis@27 --save

*****************************************************************************************************
Step 3: Set up the sample
Create a file named index.js in your working directory and copy in the following code:
(you will be using the following code as a template):


const fs = require('fs');
const readline = require('readline');
const {google} = require('googleapis');

// If modifying these scopes, delete token.json.
const SCOPES = ['https://www.googleapis.com/auth/spreadsheets.readonly'];
// The file token.json stores the user's access and refresh tokens, and is
// created automatically when the authorization flow completes for the first
// time.
const TOKEN_PATH = 'token.json';

// Load client secrets from a local file.
fs.readFile('credentials.json', (err, content) => {
  if (err) return console.log('Error loading client secret file:', err);
  // Authorize a client with credentials, then call the Google Sheets API.
  authorize(JSON.parse(content), listMajors);
});

/**
 * Create an OAuth2 client with the given credentials, and then execute the
 * given callback function.
 * @param {Object} credentials The authorization client credentials.
 * @param {function} callback The callback to call with the authorized client.
 */
function authorize(credentials, callback) {
  const {client_secret, client_id, redirect_uris} = credentials.installed;
  const oAuth2Client = new google.auth.OAuth2(
      client_id, client_secret, redirect_uris[0]);

  // Check if we have previously stored a token.
  fs.readFile(TOKEN_PATH, (err, token) => {
    if (err) return getNewToken(oAuth2Client, callback);
    oAuth2Client.setCredentials(JSON.parse(token));
    callback(oAuth2Client);
  });
}

/**
 * Get and store new token after prompting for user authorization, and then
 * execute the given callback with the authorized OAuth2 client.
 * @param {google.auth.OAuth2} oAuth2Client The OAuth2 client to get token for.
 * @param {getEventsCallback} callback The callback for the authorized client.
 */
function getNewToken(oAuth2Client, callback) {
  const authUrl = oAuth2Client.generateAuthUrl({
    access_type: 'offline',
    scope: SCOPES,
  });
  console.log('Authorize this app by visiting this url:', authUrl);
  const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout,
  });
  rl.question('Enter the code from that page here: ', (code) => {
    rl.close();
    oAuth2Client.getToken(code, (err, token) => {
      if (err) return console.error('Error while trying to retrieve access token', err);
      oAuth2Client.setCredentials(token);
      // Store the token to disk for later program executions
      fs.writeFile(TOKEN_PATH, JSON.stringify(token), (err) => {
        if (err) return console.error(err);
        console.log('Token stored to', TOKEN_PATH);
      });
      callback(oAuth2Client);
    });
  });
}

/**
 * Prints the item name, size, quantity in, quantity out, how many left, and price in a sample spreadsheet:
 * @see https://docs.google.com/spreadsheets/d/YourGoogleSheetIDGoesHere/edit
 * @param {google.auth.OAuth2} auth The authenticated Google OAuth client.
 */
function listMajors(auth) {
  const sheets = google.sheets({version: 'v4', auth});
  sheets.spreadsheets.values.get({
    spreadsheetId: 'YourGoogleSheetIDGoesHere',
    range: 'Class Data!A2:E',
  }, (err, res) => {
    if (err) return console.log('The API returned an error: ' + err);
    const rows = res.data.values;
    if (rows.length) {
      console.log('Name, Major:');
      // Print columns A and E, which correspond to indices 0 and 4.
      rows.map((row) => {
        console.log(`${row[0]}, ${row[4]}`);
      });
    } else {
      console.log('No data found.');
    }
  });
}

Step 4: Run the sample
Run the sample using the following command:
node .
The first time you run the sample, it will prompt you to authorize access:
a.	Browse to the provided URL in your web browser.
If you are not already logged into your Google account, you will be prompted to log in. If you are logged into multiple Google accounts, you will be asked to select one account to use for the authorization.
b.	Click the Accept button.
c.	Copy the code you're given, paste it into the command-line prompt, and press Enter.





