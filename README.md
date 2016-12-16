# TokenlyPublisherBridge
The "TokenlyPublisherBridge" server application will allow Tokenly users to publish media with the Open Index Protocol to the Florincoin Blockchain.

## Installation
Install Node.js then run the following commands to download and install the TokenlyPublisherBridge api.
```bash
$ git clone https://github.com/Tokenly/TokenlyPublisherBridge.git
$ cd TokenlyPublisherBridge
$ npm install
```

## Config
Now that you have installed TokenlyPublisherBridge you need to fill in a config file. Duplicate the `settings.example.cfg` file and name it `settings.cfg`. Fill it all in. Below is an example:
```javascript
{
	"florincoin_username": "florincoin",
	"florincoin_password": "password",
	"florincoin_rpc_ip": "127.0.0.1",
	"florincoin_rpc_port": "18322",
	"api_key": "password",
	"allow_ip": "127.0.0.1",
    "datadir": "/var/data"
}
```
*NOTE:* Please be aware that TokenlyPublisherBridge does not require you to specify the publisher address in the config as it will attempt to publish under the publisher address in the submitted artifacts. Please make sure that you register your publishers and have them available inside your local Florincoin node.

# Usage
## Running the Application
To run the TokenlyPublisherBridge application just run the following command and keep it running using something like screen.
```bash
$ node app.js
```

## Calling the API endpoint
Each of the API endpoints require POST data. ALL API endpoints require an `api-key`. Please POST the following keys inside the body:
```javascript
{
	"api-key": "j9189ijnf87yhja57287454fa659241454",
	"artifact": OIP-041-Artifact # This is where you submit the main data outlined below to the endpoints.
}
```
### Node.js Example:
```javascript
var request = require('request');
var baseURL = 'https://api.alexandria.io/alexandria/v2/search';
var options = {
	method: 'POST',
	headers: {},
	url: baseURL,
	body: JSON.stringify({
		'api-key': 'j9189ijnf87yhja57287454fa659241454',
		'artifact': {
			"oip-041": {
				"artifact": {
					"publisher": "FD6qwMcfpnsKmoL2kJSfp1czBMVicmkK1Q",
					"timestamp": 1481419390,
					"type": "music",
					"info": {
						"title": "Test",
						"description": "Test",
						"year": "2016",
						"extraInfo": {}
					},
					"storage":{
					"network": "IPFS",
					"location": "QmPukCZKeJD4KZFtstpvrguLaq94rsWfBxLU1QoZxvgRxA",
					"files": [
						{
							"dname": "Skipping Stones",
							"fame": "1 - Skipping Stones.mp3",
							"fsize": 6515667,
							"type": "album track",
							"duration": 1533.603293
						}
					]},
					"payment": {}
				}
			}
		}
	})
};

request(options, function (error, response, body) {
	if (!error && response.statusCode == 200) {
		// Handle response code
	} else {
		// Handle failure
	}
});
```

## API Endpoints
### Add Media:
`/add`: Adds a piece of Tokenly music metadata to the Florincoin blockchain via a transaction message. This method required JSON to be pushed to it in the OIP-041 format:
```javascript
{  
	"oip-041":{  
		"artifact":{  
			"publisher":"$PublisherAddress",
			"timestamp":1234567890,
			"type":"$ArtifactType",
			"info":{  
				"title":"$ArtifactTitle",
				"description":"$ArtifactDescription",
				"year":1234,
				"extraInfo":{  
					"artist":"$Creator",
					"company":"$Distributor",
					"composers":[  
						"$Composer1",
						"$Composer2"
					],
					"copyright":"",
					"usageProhibitions":"",
					"usageRights":"",
					"tags":[  
						"$ArtifactTag1",
						"$ArtifactTag2"
					]
				}
			},
			"storage":{  
				"network":"IPFS",
				"location":"$IPFSAddress",
				"files":[  
					{  
						"disallowBuy":true,
						"dname":"$DisplayName",
						"duration":123,
						"fname":"$FileName",
						"fsize":123,
						"minPlay":"$minPlayPriceUSD",
						"sugPlay":"$suggestedPlayPriceUSD",
						"promo":"$CutForPromoterSales",
						"retail":"$CutForPlatformSales",
						"ptpFT":12,
						"ptpDT":34,
						"ptpDA":56,
						"type":"$MediaType",
						"tokenlyID":"$SongTokenlyID"
					},
					{  
						"dissallowPlay":true,
						"dname":"$DisplayName",
						"duration":123,
						"fname":"$FileName",
						"fsize":123,
						"minBuy":"$minBuyPriceUSD",
						"sugBuy":"$suggestedBuyPriceUSD",
						"promo":"$CutForPromoterSales",
						"retail":"$CutForPlatformSales",
						"type":"$MediaType",
						"tokenlyID":"$SongTokenlyID"
					},
					{  
						"dname":"$DisplayName",
						"duration":123,
						"fname":"$FileName",
						"fsize":123,
						"minPlay":"$minPlayPriceFiat",
						"sugPlay":"$suggestedPlayPriceFiat",
						"minBuy":"$minBuyPriceFiat",
						"sugBuy":"$suggestedBuyPriceFiat",
						"promo":"$CutForPromoterSales",
						"retail":"$CutForPlatformSales",
						"ptpFT":12,
						"ptpDT":34,
						"ptpDA":56,
						"type":"$MediaType",
						"tokenlyID":"$SongTokenlyID"
					},
					{  
						"dname":"Cover Art",
						"fname":"$CoverArtFilename",
						"fsize":123,
						"type":"coverArt",
						"storage":{  
							"network":"HTTP",
							"location":"$ThumbnailURL"
						}
					}
				]
			},
			"payment":{  
				"fiat":"$fiat_id",
				"scale":"1000:1",
				"sugTip":[  
					123,
					123,
					123
				],
				"tokens":{  
					"btc":"$BitcoinAddress",
					"early":"",
					"mtmcollector":"",
					"mtmproducer":"",
					"happybirthdayep":"",
					"ltbcoin":""
				}
			}
		},
		"signature":"$IPFSAddress-$PublisherAddress-$timestamp"
	}
}
```
After processing the `/add` API endpoint will return a response as follows:
```javascript
{
	"success": true, 	# This variable is set dependant on if the API call was successful or not.
	"message": "tx1,tx2,tx3,tx4,tx5",		# This variable will be filled in on success. If success is false, `error` will be used instead of `message`.
}
```
### Edit Media:
`/edit`: The Edit Media endpoint accepts data in the OIP-041 Edit format (example below) along with data in the standard OIP-041 schema (same as `/add` but with an added `txid` field inside the data at `oip-041.artifact.txid`). It calculates what information is new and publish that to the Florincoin blockchain. Example OIP-041 Edit format:
```javascript
{  
    "success":true,
    "message":{  
        "oip-041":{  
            "edit":{  
                "txid":"a449b0f6a601e503e7b4fdc0ada47f55a8b2f98feb2fdb044f7a92d971ff0456", # This is the TXID for the artifact we are editting
                "timestamp":1481420001,
                "patch":{  
                    "add":[  
                        {  
                            "path":"/payment/tokens/mtcproducer",
                            "value":""
                        }
                    ],
                    "replace":[  
                        {  
                            "path":"/storage/files/3/fname",
                            "value":"birthdayepFirst.jpg"
                        },
                        {  
                            "path":"/storage/files/3/dname",
                            "value":"Cover Art 2"
                        },
                        {  
                            "path":"/info/title",
                            "value":"Happy Birthday"
                        },
                        {  
                            "path":"/timestamp",
                            "value":1481420001
                        }
                    ],
                    "remove":[  
                        {  
                            "path":"/payment/tokens/mtmproducer"
                        },
                        {  
                            "path":"/storage/files/0/sugBuy"
                        }
                    ]
                }
            }
        }
	}
}
```
After processing the `/edit` API endpoint will return a response as follows:
```javascript
{
	"success": true, 	# This variable is set dependant on if the API call was successful or not.
	"message": "tx1,tx2,tx3,tx4,tx5",		# This variable will be filled in on success. If success is false, `error` will be used instead of `message`.
}
```

### Remove Media:
`/remove`: Deactivates an artifact in LibraryD. This will cause the artifact to stop showing up in the browser, however it does NOT remove the previously published data from the blockchain. Any information previously published will be accessable for as long as Florincoin exists. This endpoint accepts data in the following format:
```javascript
{
	"txid": "XXXXXXXXX", 							# The ID of the artifact to be unlisted.
 	"artifactTitle": "Happy Birthday EP",			# The artifact title is included as a further backup measure. It is suggested that in the frontend the user is forced to type in the artifact title to unlist (Similar to Github repository deletions)
}
```
After processing the `/remove` API endpoint will return a response as follows:
```javascript
{
	"success": true, 	# This variable is set dependant on if the API call was successful or not.
	"message": "txid"	# This variable will be filled in on success. If success is false, `error` will be used instead of `message`.
}
```

### Transfer Ownership:
`/transfer`: Changes the ownership of an artifact from Tokenly to the individual user. Once the user transfers it away from Tokenly the metadata should be updated Browser-side. Updating further metadata clientside lowers the risk of a private key getting leaked. The `transfer` API accepts data in the following format:
```javascript
{
 	"txid": "XXXXXXXXX", 								 	 # The TXID of the artifact to be transferred.
 	"originalOwnerAddress": "FLuiVU5iDQ4a6ztcpBLwBNjBisyY2DvUTV", # The Florincoin address that the artifact needs to be transferred to.
 	"newOwnerAddress": "FD6qwMcfpnsKmoL2kJSfp1czBMVicmkK1Q", # The Florincoin address that the artifact needs to be transferred to.
}
```
After processing the `/transfer` API endpoint will return a response as follows:
```javascript
{
	"success": true, 	# This variable is set dependant on if the API call was successful or not.
	"message": "txid"	# This variable will be filled in on success. If success is false, `error` will be used instead of `message`.
}
```

## License
This project will remain closed source until version 1.0 is completed. It will then be open sourced under the GNU GPL v3.0.