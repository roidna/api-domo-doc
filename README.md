# DOMO Custom Connector

DOMO [Custom Connector IDE](https://api.domo.com/builder/index.html) supports creating scripts with Javascript ES5 (EMCAScript 5) to connect and parse data from a 3rd party.

Log into DOMO and select the DOMO domain to develop against. There are 4 steps to development:

## 1. Upload Connector Icon Images
DOMO requires 4 icons for each connector -- 3 square icons of 512x512, and one large banner image of 1024x512

## 2. Configure user authentication
Depending on what type of authentication the 3rd party service requires, select the credential type (none, username/password, API key, OAuth 2.0)

Select an API Key and enter the provided Asana Personal Access Token in the configuration box on the right side. That key is stored in the following:

* `metadata.account.apikey`

Authentication needs to be validated with a HTTP request to the 3rd party service and a success callback.

* `auth.authenticationSuccess()` for success
* `auth.authenticationFailed('failed message')` for failed authentication

See `auth.js` for an example request.
 
## 3. Configure selectable reports
Create a report to store data for this connector.

## 4. Define how data is processed
Here is where the bulk of the custom connector code will be. Make `httprequest` API requests using the authentication setup above to your 3rd party endpoints, and parse returned data into the report. 

### Creating report
The DOMO report uses a `datagrid` object to generate columns and cells. Start by adding columns (with headers), then fill each row with cell values by parsing the API response.

#### Add columns
On initial parsing, add desired report columns. Usually each column corresponds to a key in the API response.

You also need to define the column's type. DOMO allows 3 different column types:

* `DATA_TYPE_INTEGER`: integer
* `DATA_TYPE_STRING`: string
* `DATA_TYPE_DATETIME`: datetime

Example column:
* `datagrid.addColumn('id', datagrid.DATA_TYPE_INTEGER)`

#### Add rows and fill cells with value
Loop through the API response and add rows with cell values. Make sure each cell corresponds to the correct column above.

* `datagrid.addCell(row.id)`

To close out a row and move to a new row, use `datagrid.endRow()`

#### Finish parsing
When parsing is complete and no more data remains, send an error state to DOMO.

`datagrid.error(0, 'no data returned')`

## 5. Submit for Publishing
When you have verified the custom connector is working, submit the connector for publishing. Select "Shared with your company only" (unless this is a public connector) and submit for DOMO review.

During this period, you can also self-publish to be used internally for a 24 hour period.
