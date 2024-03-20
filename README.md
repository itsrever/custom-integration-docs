# Custom integrations with REVER
The REVER portal can be integrated with any custom eCommerce platform with very little work required from the eCommerce side.

In order to integrate with you custom platform, REVER only needs to have access to a few endpoints that will be defined and described in this file.

These will be classfied into a few sections:
- Compulsory: those endpoints that must be implemented in order for the portal to be functional
- Optional: these endpoints will be classified by functionality.

## Compulsory
These are the required enpoints to be able to integrate our returs portal with your platform
### <code class="get">GET</code> <code> /rever/order/{order_number} </code>
This will be endpoint that will be used in order to retrieve the information of a given order once the customer starts a return in our portal.

The parameter that will be provided in this `GET` request will be the `order_number`, which is the order number that the customer is given once it's order has been completed / fulfilled by the eCommerce.

The expected fields to be received back from your platform are:

<style> 
.detail-object{
  border-bottom: 1px solid #2b3039;
  padding:13px 0px
}

details{
  padding:7px 0px
}

.detail-object summary{
  font-size: 1.1rem;
}

code{
  background-color: rgba(255,255,255, 0.05);
  color: #efefef;
  padding: 2px 5px
}

code.get{
  background-color: #211375;
  color: #b4b1ff;
  border: 1px solid #b4b1ff;
  padding: 1px 6px
}
code.post{
  background-color: #1d2b25;
  color: #00e4ad;
  border: 1px solid #00e4ad;
  padding: 1px 6px
}

code.type{
  background-color: #091f21;
  color: #049ead;
  font-weight: 600;
  font-size: 1rem;
  padding: 2px 6px
}

code.required{
  background-color: #290400;
  color: #ff6357;
  font-weight: 600;
  font-size: 1rem;
  padding: 2px 6px
}

details.detail-object details{
  margin-left: 20px
}
</style>

<details class="detail-object">
    <summary> <code>order_number</code> : <code class="type">string</code> <code class="required">required</code> </summary>
    The order identifier given to the customer
</details>

<details class="detail-object">
    <summary> <code>order_id</code> : <code class="type">string</code> <code class="required">required</code> </summary>
    The unique internal order identifier
</details>

<details class="detail-object">
    <summary> <code>shop_currency</code> : <code class="type">string</code> <code class="required">required</code> </summary>
    The default currency in you shop. All quantities in the order should be provided in this currency   
</details>

<details class="detail-object">
    <summary> <code>customer_info</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    The default currency in you shop. All quantities in the order should be provided in this currency
    <details>
      <summary> <code>email</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
      The email that has been used in the order
    </details>
    <details >
      <summary> <code>first_name</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
    </details>
    <details >
      <summary> <code>last_name</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
    </details>
    <details>
      <summary> <code>phone</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
    </details>
     <details>
      <summary> <code>currency</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The code of the currency that has been used by the customer when purchasing. In our returns portal, quantitites will be shown in this currency
    </details>    
</details>

<details class="detail-object">
    <summary> <code>shipping_address</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    Shipping address details
    <details>
      <summary> <code>address_line_1</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     This should be, either the complete address or the address information up to the house number
    </details>
    <details >
      <summary> <code>address_line_2</code> : <code class="type">string</code></summary>
      This field should contain the adderss information regarding flat number, door number, etc
    </details>
    <details >
      <summary> <code>postal_code</code> : <code class="type">int</code>
     <code class="required">required</code> </summary>
    </details>
    <details>
      <summary> <code>city</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
    </details>
    <details>
      <summary> <code>state_province</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The province or state name
    </details>
    <details>
      <summary> <code>province_code</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
      A code that identifies the province. For spanish provinces: https://es.wikipedia.org/wiki/Anexo:Provincias_de_Espa%C3%B1a_por_c%C3%B3digo_postal
    </details>
    <details>
      <summary> <code>country_code</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
    </details>
    <details>
      <summary> <code>country</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The country name
    </details>
</details>

<details class="detail-object">
    <summary> <code>billing_address</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    Shipping address details
    <details>
      <summary> <code>address_line_1</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     This should be, either the complete address or the address information up to the house number
    </details>
    <details >
      <summary> <code>address_line_2</code> : <code class="type">string</code></summary>
      This field should contain the adderss information regarding flat number, door number, etc
    </details>
    <details >
      <summary> <code>postal_code</code> : <code class="type">int</code>
     <code class="required">required</code> </summary>
    </details>
    <details>
      <summary> <code>city</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
    </details>
    <details>
      <summary> <code>state_province</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The province or state name
    </details>
    <details>
      <summary> <code>province_code</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
      A code that identifies the province. For spanish provinces: https://es.wikipedia.org/wiki/Anexo:Provincias_de_Espa%C3%B1a_por_c%C3%B3digo_postal
    </details>
    <details>
      <summary> <code>country_code</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
    </details>
    <details>
      <summary> <code>country</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The country name
    </details>
</details>

<details class="detail-object">
    <summary> <code>line_items</code> : <code class="type">list[object]</code> <code class="required">required</code> </summary>
    A list of all the items purchased in the order
    <details>
      <summary> <code>id</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     A unique ID that, when provided, allows you to identificate the specific line items from all line items and orders
    </details>
    <details>
      <summary> <code>variant_name</code> : <code class="type">string</code></summary>
      For example, if the customer has bought "REVER T-Shirt XXS" and the product is "REVER T-Shirt", then the variant name should be "XXS"
    </details>
    <details >
      <summary> <code>variant_id</code> : <code class="type">int</code>
     <code class="required">required</code> </summary>
     A unique variant identifier
    </details>
    <details>
      <summary> <code>sku</code> : <code class="type">string</code></summary>
      This field should be empty if it's not available
    </details>
    <details>
      <summary> <code>quantity</code> : <code class="type">int</code>
     <code class="required">required</code> </summary>
     Number of products purchased
    </details>
    <details>
      <summary> <code>unit_price</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
      The price of a single unit of the given product
    </details>
    <details>
      <summary> <code>unit_discount_amount</code> : <code class="type">string</code></summary>
     Discount amount applied to each unit
    </details>
    <details>
      <summary> <code>total</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The <code>line_item</code> total computed as <code>unit_price</code> * <code>quantity</code>
    </details>
    <details>
      <summary><code>subtotal</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The <code>line_item</code> subtotal computed as (<code>unit_price</code> - taxes) * <code>quantity</code>
    </details>
    <details>
      <summary> <code>is_returnable</code> : <code class="type">boolean</code>
     <code class="required">required</code> </summary>
     Some products might not be returnable du to its nature or because they have been customized. In that case, <code>is_returnable</code> should be <code>false</code>. If <code>"is_returnable" : false</code>, all the products in the given line item will be marked as non-returnable
    </details>
    <details>
      <summary> <code>product</code> : <code class="type">object</code>
     <code class="required">required</code> </summary>
     A JSON object wit details of the parent product
      <details>
        <summary> <code>id</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
      The product ID.
      </details>
      <details>
        <summary> <code>name</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
      Following the previous example of a <code>line_item</code> for "REVER T-Shirt XXS", the product name should be "REVER T-Shirt".
      </details>
      <details>
        <summary> <code>description</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
      Following the previous example of a <code>line_item</code> for "REVER T-Shirt XXS", the product name should be "REVER T-Shirt".
      </details>
      <details>
        <summary> <code>images</code> : <code class="type">list[object]</code>
        <code class="required">required</code> </summary>
        A list of product images (one is enough)
        <details>
          <summary> <code>src</code> : <code class="type">string</code>
        <code class="required">required</code> </summary>
        The image URL
        </details>
        <details>
          <summary> <code>alt</code> : <code class="type">string</code></summary>
          Alt text for the image
        </details>
      </details>
    </details>
</details>


<details class="detail-object">
    <summary> <code>associated_costs</code> : <code class="type">object</code> <code class="required">required</code> </summary>
      <details>
        <summary> <code>logistic_costs</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
        Amount paid by the user for logistics
      </details>
      <details >
        <summary> <code>order_discount_amount</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
      Total amount of discounts applied
      </details>
</details>

<details class="detail-object">
    <summary> <code>exchange_rate</code> : <code class="type">number</code></summary>
    In case you allow multicurrency in you eCommerce, this field should be added to the order details. It defines the exchange rate at the time of the purchase. It should be computed as: (<code>unit_price</code> in customer currency) / (<code>unit_price</code> in shopper currency)
</details>

<details class="detail-object">
    <summary> <code>purchased_at</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    Purchased at date in format AAAA-MM-DDThh:mm:ss+zz:zz (ISO 8601)
</details>

<details class="detail-object">
    <summary> <code>fulfilled_at</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    Purchased at date in format AAAA-MM-DDThh:mm:ss+zz:zz (ISO 8601). It can be the same of <code>purchased_at</code> if you don't differenciate between them
</details>



#### Example
The following is an example of an order according to the structure defined above:
```json
{
	"order_number": "0004\/2024",
	"order_id": "5222",
	"shop_currency": "EUR",
	"customer_info": {
		"email": "daniouyea@gmail.com",
		"first_name": "Dani",
		"last_name": "Serch",
		"phone": 686835348,
		"currency": "EUR"
	},
	"shipping_address": {
		"address_line_1": "c\/ Doctor Alcay, 16-18, Oficina D",
		"address_line_2": "",
		"postal_code": 50006,
		"city": "Zaragoza",
		"state_province": "Zaragoza",
		"province_code": "Z",
		"country_code": "ES",
		"country": "Spain"
	},
	"billing_address": {
		"address_line_1": "c\/ Doctor Alcay, 16-18, Oficina D",
		"address_line_2": "",
		"postal_code": 50006,
		"city": "Zaragoza",
		"state_province": "Zaragoza",
		"province_code": "Z",
		"country_code": "ES",
		"country": "Spain"
	},
	"line_items": [
		{
			"id": "5222-0-169",
			"variant_name": "U",
			"variant_id": "169",
			"sku": "200000000039",
			"quantity": 1,
			"unit_price": "43.44",
			"unit_discount_amount": "0.00",
			"total": "43.44",
			"subtotal": "35.90",
			"is_returnable": true,
			"product": {
				"id": "85",
				"name": "Delantal Quchillo Negro",
				"description": "Delantal negro de cocinero cruzado, cómodo y con estilo para el trabajo en la cocina. ",
				"images": [
					{
						"src": "https:\/\/qooqer.drands.ddns.net\/files\/images\/polo-delantal-quchillo32183.jpg"
					}
				]
			}
		}
	],
	"associated_costs": {
		"logistic_costs": "4.00",
		"order_discount_amount": "0.00"
	},
	"purchased_at": "2024-03-14T15:58:12+01:00",
	"fulfilled_at": "2024-03-14T15:58:12+01:00"
}
```


### <code class="post">POST</code> <code> /rever/return </code>
This enpoint will be called every time a return has been completed. This is, when the returned object has arrived to your warehouse and been approved by you in the REVER dashboard. 

At that point, you will get a <code class="post">POST</code> request to this endpoint with all the information regarding the return process that has been completed so you can reflect it in you platform.

The <code>body</code> that will be inclued in this request to your endpoint is:
<details class="detail-object">
    <summary> <code>ecommerceID</code> : <code class="type">string</code></summary>
</details>

<details class="detail-object">
    <summary> <code>orderID</code> : <code class="type">string</code></summary>
    Your internal order identifier, which was provided in the <code class="get">GET</code> request to <code> /rever/order</code>
</details>

<details class="detail-object">
    <summary> <code>currency</code> : <code class="type">string</code></summary>
    The currency that was used by the customer when purchasing
</details>

<details class="detail-object">
    <summary> <code>return_line_itms</code> : <code class="type">list[object]</code></summary>
    A list of all the line_items included in the return. This will be a subset of the <code>line_items</code> retrieved in the <code class="get">GET</code> request.
      <details>
      <summary> <code>line_item_id</code> : <code class="type">string</code></summary>
      A unique identifier that allows you to identify the given <code>line_item</code> from withing all line items and orders in your platform.
      </details>
      <details>
      <summary> <code>quantity</code> : <code class="type">int</code></summary>
      Quantity of items that were returned by the customer
      </details>
      <details>
      <summary> <code>amount_before_costs</code> : <code class="type">int</code></summary>
      The value of the return without taking into account the shipping costs
      </details>
      <details>
      <summary> <code>amount_after_costs</code> : <code class="type">int</code></summary>
      The value of the return with shipping costs apportioned between all items
      </details>
      <details>
      <summary> <code>return_status</code> : <code class="type">string</code></summary>
      The status of the return. This could be:
      <ul> 
      <li><code>APPROVED</code>: If you have approved the return</li>
      <li><code>REJECTED</code>: If you have declined the return</li>
      <li><code>MISSING</code>: If the product has been lost</li>
      </ul>
      </details>
</details>

<details class="detail-object">
    <summary> <code>shiping_cost_refund_amount</code> : <code class="type">int</code></summary>
    The amount paid for the return shipping costs
</details>

#### Example
The following is an example of how a REVER return will look like:
```json
{
    "ecommerceID" : "qooqer",
    "orderID" : "348924",
    "currency" : "EUR",
    "return_line_items" : [
        {
            "line_item_id" : "1654",
            "quantity" : 1,
            "amount_before_costs" : 26.35,
            "amount_after_costs" : 21.58,
            "return_status" : "APPROVED"
        }
    ],
    "shipping_cost_refund_amount" : 4.96
}
```

#### Response
The response provided by this endpoint after the <code class="post">POST</code> request should be:
<details class="details-object">
<summary><code>200</code></summary>
For successful <code class="post">POST</code> requests we should get back a <code>200</code> status code with the following body:<pre><code>
{
	"success": true
}
</code></pre>
</details>
<details class="details-object">
<summary><code>406</code></summary>
For unsuccessful <code class="post">POST</code> requests where the data was invalid we should get back a <code>406</code> status code with the following body:
<pre><code>
{
	"error": "Invalid data"
}
</code></pre>
</details> 