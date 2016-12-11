# TokenlyPublisherBridge
The “TokenlyPublisherBridge” server application allows Tokenly to publish media metadata to the Florincoin blockchain. This server application includes API endpoints to publish metadata, update/change metadata, removal of metadata from being listed in LibraryD, and the transfer of ownership from Tokenly and back to the original user.

## Installation
Install Node.js then run the following commands to download and install the TokenlyPublisherBridge api.
```
$ git clone https://github.com/Tokenly/TokenlyPublisherBridge.git
$ cd TokenlyPublisherBridge
$ npm install
```

# Usage
## Running the Application
To run the TokenlyPublisherBridge application just run the following command and keep it running using something like screen.
```
$ node app.js
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
	"message": "",		# This variable will be filled in on success. If success is false, `error` will be used instead of `message`.
	"txid": ['tx1', 'tx2', 'tx3', 'tx4', 'tx5'] # All of the transaction ID's from the multipart publish (if published in single part only 1 txid will be returned, however it will still be inside of an array)
}
```
### Edit Media:
`/edit`: The Edit Media endpoint accepts data in the OIP-041 Edit format (example below) along with data in the standard OIP-041 schema (same as `/add` but with an added `txid` field inside the data at `oip-041.artifact.txid`). It calculates what information is new and publish that to the Florincoin blockchain. Example OIP-041 Edit format:
```javascript
{
	"oip-041":{
		"edit":{
			"txid":"96bad8e17f908da4c695c58b0f843a03928e338b361b3035ed16a864eafc31a2", # Original/Latest TXID
			"timestamp":1234,
			"add":{
				"payment.tokens":"FREEBIEOFTHEWEEK:\"1\""
			},
			"edit":{
				"files[0].dname":"Throwing Stones",
				"files[0].fname":"1 - Throwing Stones.mp3"
			},
			"remove":[
				"tokens.LTBCOIN"
			]
		},
		"signature":"<txid-hashofalledits-timestamp>"
	}
}
```
After processing the `/edit` API endpoint will return a response as follows:
```javascript
{
	"success": true, 	# This variable is set dependant on if the API call was successful or not.
	"message": "",		# This variable will be filled in on success. If success is false, `error` will be used instead of `message`.
	"txid": ['tx1', 'tx2', 'tx3', 'tx4', 'tx5'] # All of the transaction ID's from the multipart publish (if published in single part only 1 txid will be returned, however it will still be inside of an array)
}
```

### Remove Media:
`/remove`: Deactivates an artifact in LibraryD. This will cause the artifact to stop showing up in the browser, however it does NOT remove the previously published data from the blockchain. Any information previously published will be accessable for as long as Florincoin exists. This endpoint accepts data in the following format:
```javascript
{
	"tokenlyID": "XXXXXXXXX", 						# The ID of the artifact to be unlisted.
 	"artifactTitle": "Happy Birthday EP",			# The artifact title is included as a further backup measure. It is suggested that in the frontend the user is forced to type in the artifact title to unlist (Similar to Github repository deletions)
 	"APIKey": "j9189ijnf87yhja57287454fa659241454"	# The API Key from the config. Prevents unauthorized access.
}
```
After processing the `/remove` API endpoint will return a response as follows:
```javascript
{
	"success": true, 	# This variable is set dependant on if the API call was successful or not.
	"message": ""		# This variable will be filled in on success. If success is false, `error` will be used instead of `message`.
}
```
### Transfer Ownership:
`/changeOwner`: Changes the ownership of an artifact from Tokenly to the individual user. Once the user transfers it away from Tokenly the metadata should be updated Browser-side. Updating further metadata clientside lowers the risk of a private key getting leaked. The `changeOwner`	API accepts data in the following format:
```javascript
{
 	"tokenlyID": "XXXXXXXXX", 								 # The ID of the artifact to be transferred.
 	"newOwnerAddress": "FLuiVU5iDQ4a6ztcpBLwBNjBisyY2DvUTV", # The Florincoin address that the artifact needs to be transferred to.
 	"APIKey": "j9189ijnf87yhja57287454fa659241454"			 # The API Key from the config. Prevents unauthorized access.
}
```
After processing the `/changeOwner` API endpoint will return a response as follows:
```javascript
{
	"success": true, 	# This variable is set dependant on if the API call was successful or not.
	"message": ""		# This variable will be filled in on success. If success is false, `error` will be used instead of `message`.
}
```

## Config
Info about the config file will be here.

## License
This project will remain closed source until version 1.0 is completed. It will then be open sourced under the GNU GPL v3.0.