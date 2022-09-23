# Overview  
Adyen payment apps use a tokenized payment method which is an efficient way to secure the customers’ card information, such as PAN (Payment Account Number). In a tokenized payment, a unique string of numbers (aka token) is generated and transmitted during a transaction instead of the actual PAN to make the payment more secure.  

However, there have been instances where merchants would face “failed tokenization” scenarios. Also, a token generated against a customer’s PAN at one shop of a merchant couldn’t be used at another shop of the same merchant. This means merchants having multiple shops cannot share shopper tokens across different shops.  

To cater to these problems, a new API is developed that would generate a **metaToken ID**, unique for each customer. A **metaToken** represents a collection of existing tokens of a shopper. Based on the machine learning algorithm, the existing tokens are listed in the order of their possibility of leading to successful payment.  

A metaToken enables:  
- retrying the payment using the token with the highest probability of succeeding first.
- allow tokens to be shared across multiple shops under the same company.


HTTP Method: POST/metaTokens  
Client URL: https://checkout-test.adyen.com/v68/metaTokens  
Conten Type: application/json
# Request Body  
## Parameters  
# Response Body  
## Parameters  
# Notification Message
