# Overview  
Adyen payment apps use a tokenized payment method which is an efficient way to secure the customers’ card information, such as PAN (Payment Account Number). In a tokenized payment, a unique string of numbers (aka token) is generated and transmitted during a transaction instead of the actual PAN to make the payment more secure.  

However, there have been instances where merchants would face “failed tokenization” scenarios. Also, a token generated against a customer’s PAN at one shop of a merchant couldn’t be used at another shop of the same merchant. This means merchants having multiple shops cannot share shopper tokens across different shops.  

To cater to these problems, a new API is developed that would generate a **metaToken ID**, unique for each customer. A **metaToken** represents a collection of existing tokens of a shopper. Based on the machine learning algorithm, the existing tokens are listed in the order of their possibility of leading to successful payment.  

A metaToken enables:  
- retrying the payment using the token with the highest probability of succeeding first.
- allow tokens to be shared across multiple shops under the same company.  

To make a payment with a MetaToken, the metaTokenId needs to be included when making the **POST /payments request.** First, the highest-ranking token is tried to make the payment. If it fails, the second highest-ranking token under the same metaToken is tried, and so on.   
## Notifications 
### Success Scenario  
When a metaToken ID is successfully generated, the merchant received a success notification along with the metaToken ID. 
### Failure Scenario  
In case of a technical error (for example a timeout), a metaToken will not be generated and thus no metaToken ID is sent in response. The merchant is notified of the failure by a notification message, **“Technical Error”.** The merchant should just make the API call again.  

**Note:** Based on this API testing, it was estimated that the use of meta tokens recovered a 5 - 15% increase in failed tokenized payments. 
# Document Structure  
## Authentication
_(this section explains how the connection is established with Adyen via API)_  
Each API request made to Adyen must be authenticated. API Key authentication method is used in this case. You must authenticate your request by sending an API key in an x-API-key HTTP request header.  
> -H 'x-api-key: ' \
-H 'content-type: application/json' \  
## Request  
_This section contains the information on:_  
- _how to find the resource on the internet (URL)_
- _which HTTP method is used (explains what is being done with the resource)_
- _request parameters_  
### Request Body   
> **HTTP Method:** POST /metaTokens  
    **Client URL:** https://checkout-test.adyen.com/v68/metaTokens  
    -d '{
    "shopperReference": "shopper-123",  
    "merchantAccount": "YOUR_MERCHANT_ACCOUNT"  
    }'  
### Request Parameters  
| Parameters | Presence </br> (mandatory/optional/conditional) | Value | Description |
|------------|-------------------------------------------------|-------|-------------|
|shopperReference | mandatory | _String_ |   |
|merchantAccount | mandatory | _String_ |    |  
## Response  
_This section contains information on how API is responding to the information requested by the merchant._  

| Status Code | Message | Response Body |
|------------|----------|---------------|
|200         | OK       |  { </br> "merchantAccount": "YOUR_MERCHNAT_ACCOUNT", </br> "shopperReference": "yourShopperReference", </br> "metaTokenId": "9647-2876" </br> } |
|201        | True | { </br> "live": "false", </br> "notificationItems": [ { </br> "NotificationRequestItem": { </br> "merchantAccount": "YOUR_MERCHNAT_ACCOUNT" </br> "eventCode": "META_TOKEN_CREATION", </br> "metaTokenId": "9647-2876", </br> "success": "true" </br> }} ]} | 
|500 | Technical Error | { </br> "live": "false", </br> "notificationItems": [ </br> { </br> "NotificationRequestItem": { </br>  "eventCode": "META_TOKEN_CREATION", </br> "reason": "Technical error",</br> "success": "false" </br> }}]} |  
### Response Parameters  
| Parameters | Presence </br> (mandatory/optional/conditional) | Value | Description |
|------------|-------------------------------------------------|-------|-------------|
|shopperReference | mandatory | _String_ |   |
|merchantAccount | mandatory | _String_ |    |  
|metaTokenId | conditional | _Number_ | It is only sent when the success has a value “true” i.e., since the token only exists if it is generated. | 
| eventCode |   |    |   | 
| success | conditional | _String_ |   |
| reason | conditional | _String_ | Currently, it has one possible value but we will add more informative messages (including the action merchant should take) in later versions. |  
# Questions to ask from developers  
1.	What is the name of this API?  
2.	If a shopper is shopping for the first time from a merchant, he won’t have any record of previously existing tokens, what would the API return in that case? Since, metaToken ID represents a collection of existing tokens generated against a shopper’s PAN.   
3.	What exactly is the “shopper reference”? What’s being sent against that? Is that a unique identifier of a shopper’s record other than PAN?    
4.	Can there be a case when all the tokens under metaToken are failed to make a payment? What response is sent by API then?  
5.	Please verify the parameters tables.   
6.	Can you please confirm the status codes in the API response?  
7.	A metaToken once generated is reusable?  
8.	Since this API is created for merchants to use the shopper’s token across different shops, does it mean that the token once generated against a PAN, for shopping at any shop of the merchant’s company, will be saved in the system for future use?   
9.	Since tokenization is used to secure the payment. Doesn’t it kill the purpose of tokenization by keeping the list of tokens the same under a metaToken and making it shareable across different shops? If the data of one shop is hacked, the data of the rest of the shops also become vulnerable.   
10.	It says that the token creation might take a couple of minutes or half an hour. Shouldn’t the payment be efficient? Till then the payment will not occur?  
11.	Does metaToken ever expire?   
12.	Since the metaToken is the collection of the tokens, does it make it an array? is it a finite list? What’s the maximum number of tokens does it hold?  
13.	MetaToken ID is the reference to the first index of the metaToken if it’s an array?   
