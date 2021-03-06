_Welcome to the docs site for_
# google-spreadsheet
> The most popular [Google Sheets API](https://developers.google.com/sheets/api/reference/rest) wrapper for javascript

[![NPM version](https://badge.fury.io/js/google-spreadsheet.png)](http://badge.fury.io/js/google-spreadsheet)
[![CircleCI](https://circleci.com/gh/theoephraim/node-google-spreadsheet.svg?style=svg)](https://circleci.com/gh/theoephraim/node-google-spreadsheet)
[![Known Vulnerabilities](https://snyk.io/test/github/theoephraim/node-google-spreadsheet/badge.svg?targetFile=package.json)](https://snyk.io/test/github/theoephraim/node-google-spreadsheet?targetFile=package.json)
[![NPM](https://img.shields.io/npm/dw/google-spreadsheet)](https://www.npmjs.com/package/google-spreadsheet)

- multiple auth options - API key, service account, oauth
- cell-based API - read, write, bulk-updates, formatting
- row-based API - read, update, delete (based on the old v3 row-based calls)
- managing worksheets - add, remove, resize, change title, formatting

?> 🌈 **Installation** - `npm i google-spreadsheet --save`

## Examples
_the following examples are meant to give you an idea of just some of the things you can do_

### The Basics
```javascript
const { GoogleSpreadsheet } = require('google-spreadsheet');

// spreadsheet key is the long id in the sheets URL
const doc = new GoogleSpreadsheet('<the sheet ID from the url>');

// use service account creds
await doc.useServiceAccountAuth({
  client_email: process.env.GOOGLE_SERVICE_ACCOUNT_EMAIL,
  private_key: process.env.GOOGLE_PRIVATE_KEY,
});
// OR load directly from json file if not in secure environment
await doc.useServiceAccountAuth(require('./creds-from-google.json'));
// OR use API key -- only for read-only access to public sheets
doc.useApiKey('YOUR-API-KEY');

await doc.loadInfo(); // loads document properties and worksheets
console.log(doc.title);
await doc.updateProperties({ title: 'renamed doc' });

const sheet = doc.sheetsByIndex[0]; // or use doc.sheetsById[id]
console.log(sheet.title);
console.log(sheet.rowCount);

// adding / removing sheets
const newSheet = await doc.addSheet({ title: 'hot new sheet!' });
await newSheet.delete();
```
More info:
- [GoogleSpreadsheet](classes/google-spreadsheet)
- [GoogleSpreadsheetWorksheet](classes/google-spreadsheet-worksheet)
- [Authentication](getting-started/authentication)



### Working with rows
```javascript
// create a sheet and set the header row
const sheet = await doc.addSheet({ headers: ['name', 'email'] });

// append rows
await sheet.addRow({ name: 'Larry Page', email: 'larry@google.com' });
await sheet.addRow({ name: 'Sergey Brin', email: 'sergey@google.com' });

// read rows
const rows = await sheet.getRows(); // can pass in { limit, offset }

// read/write row values
console.log(rows[0].name); // 'Larry Page'
rows[1].email = 'sergey@abc.xyz'; // update a value
await rows[1].save(); // save updates
await rows[1].delete(); // delete a row
```
More info:
- [GoogleSpreadsheetWorksheet > Working With Rows](classes/google-spreadsheet-worksheet#working-with-rows)
- [GoogleSpreadsheetRow](classes/google-spreadsheet-row)



### Working with cells
```javascript
await sheet.loadCells('A1:E10'); // loads a range of cells
console.log(sheet.cellStats); // total cells, loaded, how many non-empty
const a1 = sheet.getCell(0, 0); // access cells using a zero-based index
const c6 = sheet.getCellByA1('C6'); // or A1 style notation
// access everything about the cell
console.log(a1.value);
console.log(a1.formula);
console.log(a1.formattedValue);
// update the cell contents and formatting
a1.value = 123.456;
c6.formula = '=A1';
a1.textFormat = { bold: true };
c6.note = 'This is a note!';
await sheet.saveUpdatedCells(); // save all updates in one call
```
More info:
- [GoogleSpreadsheetWorksheet > Working With Cells](classes/google-spreadsheet-worksheet#working-with-cells)
- [GoogleSpreadsheetCell](classes/google-spreadsheet-row)



## Why?
?> **This module provides an intuitive wrapper around Google's API to simplify common interactions**

While Google's v4 sheets api is much easier to use than v3 was, the official [googleapis npm module](https://www.npmjs.com/package/googleapis) is a giant meta-tool that handles _every Google product_. The module and the API itself are awkward and the docs are pretty terrible, at least to get started.

**In what situation should you use Google's API directly?**<br>
This module makes trade-offs for simplicity of the interface.
Google's API provides a mechanism to make many requests in parallel, so if speed and efficiency is extremely important to your use case, you may want to use their API directly. There are also several features of their API that are not implemented here yet.


## Support & Contributions

This module was written and is actively maintained by [Theo Ephraim](https://theoephraim.com).

**Are you actively using this module for a commercial project? Want to help support it?**<br>
[Buy Theo a beer](https://paypal.me/theoephraim)

#### Sponsors

None yet - get in touch!

#### Contributing

Contributions are welcome, but please follow the existing conventions, use the linter, add relevant tests, add relevant documentation.

These docs are generated using [docsify](https://docsify.js.org). To preview and run locally so you can make edits, run `npm run docs:preview` and head to http://localhost:3000
The content lives in markdown files in the docs folder.

## License
This is free and unencumbered public domain software. For more info, see https://unlicense.org.
