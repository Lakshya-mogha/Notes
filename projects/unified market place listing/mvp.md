For both Amazon and Flipkart, we need approval for a third-party application. Since we can't get approval right away,so what we can do is:

1. Focus on providing Excel sheets and generating data.
2. Making sure that what we generate is good to be listed.
3. Work on doing the provider adapters in parallel.
Once we have the approval, we can jump on to doing the auto listing part.

# code structure

 It will consist of three components:

1. The web UI with Next.js
2. A Python backend. Within that Python backend, there will be two things:
	1. An API structure for doing every processing, AI calling, data storing, attributes, and checking for errors
	2. The connectors for the providers, such as Amazon and Flipkart. Only those connectors will be able to call those providers.

# Connectors

It will be a separate folder within the Python backend, which only consists of provider APIs and function to be used within the code. 

# Auth

Since we are catering to both agency owners, freelancers, and individual sellers, we need to figure out a way during the onboarding for our platform to determine what kind of seller you are, and then only let you add multiple selling accounts.

### onborading 

The onboarding form should consist of:

- What kind of seller are you? 
	- [ ] agency 
	- [ ] individual 
	- [ ] freelancer

#### Individuals 
- username
- email
- password
- the company name.
- what product or what niche they do sell in
- How many monthly SKUs they have.
- where they heard about us
- which platforms they use (it will be like a checkbox: Amazon, Flipkart,etc)
#### Agencies/freelancers
- How many brands are you working with? 

And at last, we'll prompt them to connect their accounts, Amazon and Flipkart. It should be like a login. It will be like a login where they can click on a button, and it will take them to their seller account. It will connect it to our third-party application once we are verified.

For agencies/freelancers, it will be like a form where they can add a name for the brand they are working with, and then it will be a login which they can do or a token field which they can fill out from their seller applications. 

![[Pasted image 20260903182022.png]]

# Fiplkart

Flipkart authorization URL. The client ID is our ID given to us by Flipkart. The redirect URI will be given by us, where the user should be redirected after the login, and the state is A random value which we add to the API and flip function just to verify if it is the same request or not 

```curl 
https://api.flipkart.net/oauth-service/oauth/authorize?
client_id=<client-id>&
redirect_uri=<redirect-uri>&
response_type=code&
scope=Seller_Api&
state=<state>
```

By using the above API: We get the authorization code, and using this authorization code, we have to generate an access token using the below API. 

```
https://api.flipkart.net/oauth-service/oauth/token?
redirect_uri=<redirect-uri>&
grant_type=authorization_code&
state=<state>&
code=<code>
```

**Sample response**

```json
{
  "access_token": "f638949a­c979­4172­b33c­23311a168647",
  "token_type": "bearer",
  "refresh_token": "860e03da­d58a­4988­9149-a4a7f365bba1",
  "expires_in": 5183999,
  "scope": "Seller_Api",
  "refresh_token_expires_in": 10703805
}
```

The access token expires in 60 days, and the refresh token expires in 180 days. We can regenerate the refresh token using the access token. 

[look for third party application for more](https://seller.flipkart.com/api-docs/FMSAPI.html)

# Amazon 
The Amazon Flow is exactly the same as the Flipkart. [For documentation](https://developer-docs.amazon/sp-api/docs/website-authorization-workflow)

# Fields necessary for listing an item

- [Flipkart lisitng docs](https://seller.flipkart.com/api-docs/listing-api-docs/LMAPIRef.html#post-listings-v3)

| Field                              | Type                  | Occurrence  | Description                                                                                                                                                                                                                                                                                             |
| ---------------------------------- | --------------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| product_id                         | String                | Mandatory   | This is the product’s identifier in the Flipkart Marketplace. Length Range -> 13 to 16 characters.                                                                                                                                                                                                      |
| price.mrp                          | Non-negative Integer  | Mandatory   | The maximum retail price for the product                                                                                                                                                                                                                                                                |
| price.sellingPrice                 | Non-negative Integer  | Mandatory   | Your selling price for the product                                                                                                                                                                                                                                                                      |
| tax.hsn                            | String                | Mandatory   | The Harmonized System Nomenclature for the product used to determine applicable tax rate                                                                                                                                                                                                                |
| tax.tax_code                       | String                | Mandatory   | Flipkart’s tax code which decides the goods and services tax for the listing. Each tax_code internally maps to a Goods Services Tax (GST). Eg, tax_code GST_0 maps to 0% tax and GST_APPAREL maps to GST of apparels according to the final cart value. All tax-codes are available at MyListings page. |
| listing_status                     | Enumeration           | Mandatory   | Controls the listing’s visibility in the marketplace. Values can be ACTIVE, INACTIVE.                                                                                                                                                                                                                   |
| shipping_fees.local                | Non-negative Integer  | Conditional | Delivery charge you want charge a buyer in the same city for listings which are not Flipkart Assured                                                                                                                                                                                                    |
| shipping_fees.zonal                | Non-negative Integer  | Conditional | Delivery charge you want charge a buyer outside your zone for listings which are not Flipkart Assured                                                                                                                                                                                                   |
| shipping_fees.national             | Non-negative Integer  | Conditional | Delivery charge you want charge a buyer outside your zone for listings which are not Flipkart Assured                                                                                                                                                                                                   |
| fulfillment_profile                | Enumeration           | Mandatory   | Fulfillment method for the listing                                                                                                                                                                                                                                                                      |
| fulfillment.dispatch_sla           | None-negative Integer | Conditional | Time required to keep the product ready for dispatch                                                                                                                                                                                                                                                    |
| fulfillment.shipping_provider      | Enumeration           | Mandatory   | Information on who will ship the item to the customer                                                                                                                                                                                                                                                   |
| fulfillment.procurement_type       | Enumeration           | Mandatory   | Information on how the inventory is procured by the seller to fulfill an order                                                                                                                                                                                                                          |
| packages                           | Array                 | Conditional | The physical package configuration of the lot.                                                                                                                                                                                                                                                          |
| packages[].name                    | String                | Mandatory   | Your identifier for the package. Max length: 64                                                                                                                                                                                                                                                         |
| packages[].dimensions              | Dimension             | Conditional | The dimension of the package                                                                                                                                                                                                                                                                            |
| packages[].dimensions.length       | Non-negative Decimal  | Mandatory   | The length of the package in centimeters.                                                                                                                                                                                                                                                               |
| packages[].dimensions.breadth      | Non-negative Decimal  | Mandatory   | The breadth of the package in centimeters.                                                                                                                                                                                                                                                              |
| packages[].dimensions.height       | Non-negative Decimal  | Mandatory   | The height of the package in centimeters.                                                                                                                                                                                                                                                               |
| packages[].weight                  | Non-negative Decimal  | Conditional | The weight of the package. It is mandatory to specify it in case dimensions are defined.                                                                                                                                                                                                                |
| packages[].description             | String                | Conditional | The description of the package. It can be used to display additional information to the customer on the product page. Max length: 4000                                                                                                                                                                  |
| packages[].handling                | Handling              | Conditional | Handling information to be transmitted to the configured logistics provider.                                                                                                                                                                                                                            |
| packages[].handling.fragile        | Boolean               | Conditional | Signals that the package contains fragile items.                                                                                                                                                                                                                                                        |
| packages[].notional_value          | NotionalValue         | Conditional | The part-value of the package. It is mandatory to define it if the lot has multiple packages.                                                                                                                                                                                                           |
| packages[].notional_value.amount   | Non-negative Integer  | Mandatory   | The value amount.                                                                                                                                                                                                                                                                                       |
| packages[].notional_value.unit     | Enumeration           | Mandatory   | The unit of the amount.                                                                                                                                                                                                                                                                                 |
| locations                          | Array                 | Mandatory   | Your selling locations for this listing.                                                                                                                                                                                                                                                                |
| locations[].id                     | String                | Mandatory   | The location ID obtained via the Onboarding API.                                                                                                                                                                                                                                                        |
| locations[].status                 | Enumeration           | Mandatory   | Controls the fulfillment of the product at this location. If disabled, then orders will not be fulfilled from this location.                                                                                                                                                                            |
| locations[].inventory              | Non-negative Integer  | Mandatory   | The number of items you have in stock.                                                                                                                                                                                                                                                                  |
| address_label.manufacturer_details | Array                 | Mandatory   | Name and address of the manufacturer                                                                                                                                                                                                                                                                    |
| address_label.importer_details     | Array                 | Conditional | Name and address of the importer if the product is being imported. Mandatory if country of origin is not (IN)India.                                                                                                                                                                                     |
| address_label.packer_details       | Array                 | Mandatory   | Where manufacturer is not the packer, name and address of packer                                                                                                                                                                                                                                        |
| address_label.countries_of_origin  | Array                 | Mandatory   | ISO-2 code of the Country of Origin or manufacturing or country of assembly in case of imported products (Eg: IN for India, US for United States Of America)                                                                                                                                            |
| dating_label.mfg_date              | Positive Integer      | Conditional | Date in EPOCH (seconds) on which the product was manufactured or prepacked or imported. For products where day is not available, please put the manufacturing date as 1st of the month. Eg, 01/01/2018 converted to EPOCH (1514745000) for a product manufactured in Jan 2018.                          |
| dating_label.shelf_life            | Positive Integer      | Conditional | Duration (in seconds) from the date of manufacturing beyond which the product shall not have the quality attributes normally expected by the consumers and the commodity should not be regarded as marketable. Maximum allowed value is 155520000 seconds (60 months)                                   |

For Amazon, it is a bit different. There is no defined table or structure in the documentation. Rather, we have to call an API with the product type, and then only we can get the definitions that we need to send for the listing items.[Get product definitions](https://developer-docs.amazon/sp-api/docs/search-available-product-type-definitions)

# Listing

``` curl
#Amazon
curl --request PUT \
     --url https://sellingpartnerapi-na.amazon.com/listings/2020-09-01/items/sellerId/sku \
     --header 'accept: application/json' \
     --header 'content-type: application/json'
``` 

```curl
# Flipkart
curl -H"Authorization:Bearer <token>"-H"Content-Type: application/json" -d '{"filter" :{}}' https://api.flipkart.net/listings/v3
```

