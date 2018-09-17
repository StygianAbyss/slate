---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - XML
  - Ruby
  - Python
  - PHP
  - Java
  - .NET


toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

The cnpAPI data format supports two types of transaction submission methods: Online and Batch. With the Online method, you submit each transaction independently and receive a response in real-time. Typically, merchants use the Online method for Authorization transactions, as well as transactions available only via Online (for example, Void). The Batch method enables you to submit multiple transactions simultaneously in a single Session file. Vantiv recommends the Batch method for all transaction submissions except Authorizations and the transaction available only via Online.

While the cnpAPI is XML, we also examples in the various languages supported by our SDKs: Java, Python, .NET, PHP and Ruby.

# Authorization Transactions

The Authorization transaction enables you to confirm that a customer submitted a valid payment method and has sufficient funds to purchase the goods or services they ordered. Including the Bill to Address information triggers an AVS check.

##Authorization Elements

Element | Required (Y, N, C) | Description
--------- | ------- | -----------
orderId| Y | A merchant-assigned value representing the order in the merchant’s system.
amount| Y | The amount of the transaction.
orderSource| Y | Defines the order entry source for the type of transaction. Possible values are: 3dsAuthenticated, 3dsAttempted, echeckppd, ecommerce, installment, mailorder, rrecurring, retail, telephone, recurringtel, applepay, and androidpay. 
customerInfo | N |The parent of several child elements use to define customer information.
billToAddress | N | Contains several child elements that define the postal mailing address (and telephone number) used for billing purposes. Worldpay recommends you include the address and zip for Address Verification purposes.
shipToAddress | N | Contains several child elements that define the postal mailing address (and telephone number) used for shipping purposes. 
card, token, paypal, paypage, applepay | Y | Defines the method of payment (card, paypal, or applepay), or use of a low or high value token.
cardholderAuthentication | N | Contains child elements that define the authentication code and customer IP Address (Paypal transactions).
pos | N | Contains child elements used to specify information required when submitting transactions from point of sale terminals.
customBilling | N | Defines soft/custom billing descriptor information for the transaction.


> To authorize, use this code:

```ruby
require 'LitleOnline'
include LitleOnline

#Authorization
auth_info = {
  'orderId' => '1',
  'amount' => '10010',
  'orderSource'=>'ecommerce',
  'billToAddress'=>{
  'name' => 'John Smith',
  'addressLine1' => '1 Main St.',
  'city' => 'Burlington',
  'state' => 'MA',
  'zip' => '01803-3747',
  'country' => 'US'},
  'card'=>{
  'number' =>'4**************9',
  'expDate' => '0112',
  'cardValidationNum' => '349',
  'type' => 'VI'}
}
auth_response = LitleOnlineRequest.new.authorization(auth_info)

#display results
puts "Response: " + auth_response.authorizationResponse.response
puts "Message: " + auth_response.authorizationResponse.message
puts "Litle Transaction ID: " + auth_response.authorizationResponse.litleTxnId

```

```python
from litleSdkPython.litleOnlineRequest import *

config = Configuration()
config.setUser("User")
config.setPassword("Pass")
config.setMerchantId("123")
config.setUrl("Sandbox")
config.setProxy("")

#Authorization
auth = litleXmlFields.authorization()
auth.orderId = '1'
auth.amount = 10010
auth.orderSource = 'ecommerce'
contact = litleXmlFields.contact();
contact.name = "John Smith"
contact.addressLine1 = "1 Main St."
contact.city = "Burlington"
contact.state = "MA"
contact.zip = "01803-3747"
contact.country = "USA"
auth.billToAddress = contact
card = litleXmlFields.cardType()
card.number = "4457010000000009"
card.expDate = "0121"
card.cardValidationNum = "349"
card.type = 'VI'
auth.card = card

litleXml = litleOnlineRequest(config)
response = litleXml.sendRequest(auth)

#display results
print "Response: " + response.response
print "Message: " + response.message
print "LitleTransaction ID: " + str(response.litleTxnId)
```

```xml
<cnpOnlineRequest version="12.0" xmlns="http://www.vantivcnp.com/schema" merchantId="100">
	<authentication>
		<user>User Name</user>
		<password>Password</password>
	</authentication>
	<authorization id="834262" reportGroup="ABC Division" customerId="038945">
		<orderId>65347567</orderId>
		<amount>40000</amount>
		<orderSource>3dsAuthenticated</orderSource>
		<billToAddress>
			<name>John Smith</name>
			<addressLine1>100 Main St</addressLine1>
			<city>Boston</city>
			<state>MA</state>
			<zip>12345</zip>
			<email>jsmith@someaddress.com</email>
			<phone>555-123-4567</phone>
		</billToAddress>
		<card>
			<type>VI</type>
			<number>4000000000000002</number>
			<expDate>1209</expDate>
			<cardValidationNum>555</cardValidationNum>
		</card>
		<cardholderAuthentication>
			<authenticationValue>BwABBJQ1gJDUCAAAAAAA=</authenticationValue>
			<authenticationTransactionId>gMV75TmjAgk=		</authenticationTransactionId>
		</cardholderAuthentication>
	</authorization>
</cnpOnlineRequest>
```

```java

import com.litle.sdk.generate.*;

//Authorization
public class AuthExample {
    public static void main(String[] args) {
        Authorization auth = new Authorization();
        auth.setOrderId("1");
        auth.setAmount(10010L);
        auth.setOrderSource(OrderSourceType.ECOMMERCE);
        Contact billToAddress = new Contact();
        billToAddress.setName("John Smith");
        billToAddress.setAddressLine1("1 Main St.");
        billToAddress.setCity("Burlington");
        billToAddress.setState("MA");
        billToAddress.setCountry(CountryTypeEnum.US);
        billToAddress.setZip("01803-3747");
        auth.setBillToAddress(billToAddress);
        CardType card = new CardType();
        card.setNumber("375001010000003");
        card.setExpDate("0112");
        card.setCardValidationNum("349");
        card.setType(MethodOfPaymentTypeEnum.AX);
        auth.setCard(card);

        AuthorizationResponse response = new LitleOnline().authorize(auth);
        //Display Results
        System.out.println("Response: " + response.getResponse());
        System.out.println("Message: " + response.getMessage());
        System.out.println("Litle Transaction ID: " + response.getLitleTxnId());
    }
}
```

```.NET
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Litle.Sdk;

    class Example
    {
        [STAThread]
        public static void Main(String[] args)
        {
            LitleOnline litle = new LitleOnline();
            authorization authorization = new authorization();
            authorization.orderId = "1";
            authorization.amount = 1495;
            authorization.orderSource = orderSourceType.ecommerce;
            contact contact = new contact();
            contact.name = "John Smith";
            contact.addressLine1 = "1 Main St.";
            contact.city = "Burlington";
            contact.state = "MA";
            contact.zip = "01803-3747";
            contact.country = countryTypeEnum.US;
            authorization.billToAddress = contact;
            cardType card = new cardType();
            card.type = methodOfPaymentTypeEnum.VI;
            card.number = "4457010000000009";
            card.expDate = "0112";
            card.cardValidationNum = "349";
            authorization.card = card;

            authorizationResponse response = litle.Authorize(authorization);

            //Display Results
            Console.WriteLine("Response: " + response.response);
            Console.WriteLine("Message: " + response.message);
            Console.WriteLine("Litle Transaction Id: " + response.litleTxnId);
            Console.ReadLine();
        }
}
```


