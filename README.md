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
`/add`: Adds a piece of Tokenly music metadata to the Florincoin blockchain via a transaction message. This method required JSON to be pushed to it in the following format:
```javascript
{

}
```
After processing the `/add` API endpoint will return a response as follows:
```javascript
{
	"success": true, 	# This variable is set dependant on if the API call was successful or not.
	"error": "",		# This variable will be filled in on errors.
	"oip-data": {		# This contains the full artifact that was published to the add endpoint.
		"oip-artifact-041": {
			"publisher": "F97Tp8LYnw94CpXmAhqACXWTT36jyvLCWx",
			"timestamp": 1470269387,
			"type": "music",
			"info": {
				"title": "Happy Birthday EP",
				"year": 2016,
				"description": "this is the second organically grown, gluten free album released by Adam B. Levine - contact adam@tokenly.com with questions or comments or discuss collaborations.",
				"extra-info": {
					"artist": "Adam B. Levine",
					"company": "",
					"composers": "Adam B. Levine",
					"copyright": "",
					"tokenly_ID": "",
					"usageProhibitions": "",
					"usageRights": "",
					"tags": [],
					"files": [
						{
							"dname": "Skipping Stones",
							"fame": "1 - Skipping Stones.mp3",
							"fsize": 6515667,
							"type": "album track",
							"duration": 1533.603293,
							"sugPlay": 100,
							"minPlay": null,
							"sugBuy": 750,
							"minBuy": 500,
							"promo": 10,
							"retail": 15,
							"ptpFT": 10,
							"ptpDT": 20,
							"ptpDA": 50
						},
						{
							"dname": "Lessons",
							"fame": "2 - Lessons with intro.mp3",
							"fsize": 6515667,
							"type": "album track",
							"duration": 1231.155243,
							"disallowPlay": 1,
							"sugBuy": 750,
							"minBuy": 500,
							"promo": 10,
							"retail": 15,
							"ptpFT": 10,
							"ptpDT": 20,
							"ptpDA": 50
						},
						{
							"dname": "Born to Roam",
							"fame": "3 - Born to Roam.mp3",
							"fsize": 6515667,
							"type": "album track",
							"duration": 2374.550714,
							"sugPlay": 100,
							"minPlay": 50,
							"disallowBuy": 1,
							"promo": 10,
							"retail": 15,
							"ptpFT": 10,
							"ptpDT": 20,
							"ptpDA": 50
						},
						{
							"dname": "Cover Art",
							"fname": "birthdayepFINAL.jpg",
							"type": "coverArt",
							"disallowBuy": 1
						}
					],
					"file_network": "IPFS",
					"file_location": "QmPukCZKeJD4KZFtstpvrguLaq94rsWfBxLU1QoZxvgRxA"
				}
			},
			"payment": {
				"fiat": "USD",
				"tokens": {
					"MTMCOLLECTOR": "",
					"MTMPRODUCER": "",
					"HAPPYBDAYEP": "",
					"EARLY": "",
					"LTBCOIN": "",
					"BTC": "1GMMg2J5iUKnDf5PbRr9TcKV3R6KfUiB55"
				}
			}
		},
		"signature": "H27r7UxUb8BozjEvV0v++nCyRI7S6yyroeKCJQpgU5NO3CP6FpXWs5kCxy8vhmMhbtpj/FMj+8s3+updw7g+bmE="
	}
}
```
### Update Media:
`/update`: The Update Media endpoint accepts the same data as `/add` above. It however will decide what information is new and publish that to the Florincoin blockchain. Example input data:
```javascript
{
	
}
```
The Update Media endpoint returns the same information as the `/add` endpoint above.

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
	"error": ""			# This variable will be filled in on errors.
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
	"error": ""			# This variable will be filled in on errors.
}
```

## Config
Info about the config file will be here.

## License
This project will remain closed source until version 1.0 is completed. It will then be open sourced under the GNU GPL v3.0.