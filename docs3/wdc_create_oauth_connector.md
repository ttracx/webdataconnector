---
title: Create an OAuth Connector
layout: docs3
---
{% include prelim_note.md %}

To create your OAuth connector, we recommend that you first create a sample OAuth connector and edit the generated files. It's easier to get all the files and directory structure your connector needs by just using an existing example.

* TOC
{:toc}

To create your OAuth connector, do the following steps.
## Step 1: Create a boilerplate OAuth connector

1. Enter the following command to create the connector:

   ```
   taco create myOAuthConnector --oauth
   ```

   This creates a directory with the earthquake data boilerplate code, which is included with the toolkit.

1. Change directories to the myOAuthConnector directory.
   ```
   cd myOAuthConnector
   ```
   
1. Build the connector by entering the following command:

   ```
   taco build
   ```
   This command clears any previous or existing build caches, then installs the dependencies, then builds the frontend code and the backend code (handlers), then copies the connector.json file (the configuration file).
   
## Step 2: Configure your connector's properties

In your new OAuth connector directory, find and open the `connector.json` file. 
```json
{
	"name": "OAuth Sample",
	"version": "1",
	"tableau-version": {
		"min": "2022.2",
		"max": "*"
	},
	"vendor": {
		"name": "Salesforce",
		"support-link": "https://www.tableau.com/support",
		"email": "vendor@tableau.com"
	},
	"auth": {
		"type": "oauth2",
		"oauth": {
			"clientIdDesktop": "Q01HCP3G2XBEFE3SYRN4LKTGOJGUIYP3GSLWGKWOSKUFMT1R",
			"clientSecretDesktop": "SNMZWOONOPQM132KLLXBABMTYBSQNKSCXTBHWCSK5HIZAGGV",
			"redirectUrisDesktop": [
				"http://localhost:55555/Callback"
			],
			"authUri": "https://foursquare.com/oauth2/authenticate",
			"tokenUri": "https://foursquare.com/oauth2/access_token",
			"userInfoUri": "",
			"scopes": [
				"authorization_code"
			],
			"capabilities": {
				"OAUTH_CAP_CLIENT_SECRET_IN_URL_QUERY_PARAM": true,
				"CAP_FIXED_PORT_IN_CALLBACK_URL": false,
				"CAP_PKCE_REQUIRES_CODE_CHALLENGE_METHOD": true,
				"CAP_REQUIRE_PKCE": true
			},
			"accessTokenResponseMaps": {
				"ACCESSTOKEN": "access_token",
				"REFRESHTOKEN": "refresh_token",
				"access-token-issue-time": "issued_at",
				"access-token-expires-in": "expires_in"
			}
		}
	},
	"permission": {
		"api": {
			"https://api.foursquare.com/v2/*": [
				"GET",
				"POST",
				"HEAD"
			]
		}
	},
	"window": {
		"height": 900,
		"width": 770
	},
	"config": {
		"apiURL": "https://api.foursquare.com/v2/venues/search"
	}
}
```
* Requires auth detail
Make the following changes:

1. Change the general properties.

   | Name | Value |
   |------|-------|
   | name | Your connector's name |
   | version | Your connector's version |
   | min | The earliest Tableau version your connector supports |
   | max | The latest Tableau version your connector supports. Enter `*` for the current version. |

1. Change the company properties.

   | Name | Value |
   |------|-------|
   | vendor.name | Your company name |
   | vendor.support-link | Your company's URL |
   | vendor.email | Your company's email |

1. Change the permissions.

   | Name | Value |
   |------|-------|
   | permission.api | The URI for the API that the connector is allowed to access, along with the methods (POST, GET, PUT, PATCH, DELETE) that the connector is allowed to use. |

1. Set the authentication values.

   | Name | Value |
   |------|-------|
   | auth.type | Set to `oauth2` |
   | auth.oauth | ???
   
   For more information about authentication, see [Authentication]({{ site.baseurl }}/docs3/wdc_authentication) topic.

1. Change the HTML pane size.

   | Name | Value |
   |------|-------|
   | window.height | The height of the connector HTML pane |
   | window.width | The width of the connector HTML pane |

## Step 3: Create the user interface
When you open a web data connector in Tableau, the connector displays an HTML page that links to your JavaScript code and to your connector's handlers.
Optionally, this page can also display a user interface for your users to select the data that they want to download.

To create a user interface for your connector, open the `/app/index.html` file. 
```html
<!DOCTYPE html>
<html>

<head>
  <title>OAuth Sample Connector</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <meta http-equiv="Cache-Control" content="no-store" />
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet"
    integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
  <link rel="stylesheet" href="app.css">

</head>

<body>
  <div id="root"></div>
  <script type="module" src="./App.tsx"></script>
</body>

</html>
```

Let's run through what the code is doing. Skipping over the standard markup for an HTML page, notice the
following between the `head` tags:

* The `meta` tag prevents your browser from caching the page.
* The `bootstrap.min.css` and `app.css` files are used to simplify styling and formatting.
* The `App.txs` file is the code for your connector.

## Step 4: Edit the connector object
Now that you've created a user interface, it's time to edit the JavaScript code for the connector's button. First, open the `/app/App.txs` file. 

``` js
import ReactDOM from 'react-dom/client'
import React from 'react'
import ConnectorView from './components/ConnectorView'

const root = ReactDOM.createRoot(document.getElementById('root') as HTMLElement)
root.render(
  <React.StrictMode>
    <ConnectorView />
  </React.StrictMode>
)
```
Some notes about the code:
* ???

## Step 5: Update the fetcher file
If your data is complex and needs preprocessing, use the Taco Toolkit library to prepare your data.
The following is the default code that the fetcher uses to get the data:

```js
import { Fetcher, AjaxRequest, ExtractorContext, HandlerInput } from 'taco-toolkit/handlers'

export default class MyFetcher extends Fetcher {
  async fetch(request: HandlerInput, context: ExtractorContext) {
    const headers = { authorization: 'Bearer ' + context.connector.secrets?.accessToken }
    return await await AjaxRequest.getArrayBuffer(request.data.url, { headers })
  }
}
```

## Step 6: Configure how the data is presented

Now you must define how
you want to map the data to one or more or tables. This mapping of data is done in the schema.

To decide how to map your data, look at your data source. When you're done looking at the summary of the JSON data source, make the necessary edits to structure the returned data.

``` js
import { Parser, DataContainer, HandlerInput, ExcelParser } from 'taco-toolkit/handlers'

export default class MyParser extends Parser {
  async parse(fetcherResult: any, input: HandlerInput): Promise<DataContainer> {
    const container = await ExcelParser.parse(fetcherResult)
    return container
  }
}
```

Some notes:
* You don't need to write a custom parser for CSV data or for Excel data. The Taco Toolkit contains these parsers. For more information, see ???

## Step 7: Build your connector
Enter these commands to build, pack, and run your new connector:
```
taco build
```
```
taco pack
```
```
taco run --desktop
```
