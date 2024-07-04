# Custom integrations with REVER

The REVER portal can be integrated with any custom eCommerce platform with very little work required from the eCommerce side.

In order to integrate with you custom platform, REVER only needs to have access to a few endpoints that will be defined and described in this file.

These will be classfied into a few sections:

- Compulsory: those endpoints that must be implemented in order for the portal to be functional
- Optional: these endpoints will be classified by functionality.

## Authentication

In order to ensure that the data of our customers is safe, authentication is required for all the endpoints. Thus, all endpoints should require a header with the following structure:

```
X-rever-api-key : <api_key>
```

You can define <code>api-key</code> as you wish, and its value should be shared with us at the beginning of the integration process via a one time scret link.

## Compulsory

These are the required enpoints to be able to integrate our returs portal with your platform

### <code class="get">GET</code> <code> /rever/orders/{order_id} </code>

This will be endpoint that will be used in order to retrieve the information of a given order at various stages of the return process.

The expected fields to be received back from your platform are:

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
    The default currency in your shop. All quantities in the order should be provided in this currency   
</details>

<details class="detail-object">
    <summary> <code>customer_info</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    An object with all the customer information associted with the order
    <details>
      <summary> <code>email</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
      The email that has been used in the order
    </details>
    <details >
      <summary> <code>first_name</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The first name of the customer who has made the purchase
    </details>
    <details >
      <summary> <code>last_name</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The last name of the customer who has made the purchase
    </details>
    <details>
      <summary> <code>phone</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     Phone number of the customer with the correct country code
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
      <summary> <code>first_name</code> : <code class="type">string</code>
    </summary>
     The first name for the shipping address. If empty, the <code>customer</code>'s first name will be used
    </details>
    <details>
      <summary> <code>second_name</code> : <code class="type">string</code>
    </summary>
     The second name for the shipping address. If empty, the <code>customer</code>'s second name will be used
    </details>
    <details>
      <summary> <code>email</code> : <code class="type">string</code>
    </summary>
     The email for the shipping address. If empty, the <code>customer</code>'s email will be used
    </details>
    <details>
      <summary> <code>phone</code> : <code class="type">string</code>
    </summary>
     The phone for the shipping address. If empty, the <code>customer</code>'s phone will be used
    </details>
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
     The postal code of the address
    </details>
    <details>
      <summary> <code>city</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The city name
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
     The country code of the country: https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes
    </details>
    <details>
      <summary> <code>country</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The country name of the address.
    </details>
</details>

<details class="detail-object">
    <summary> <code>billing_address</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    Billing address details
    <details>
      <summary> <code>first_name</code> : <code class="type">string</code>
    </summary>
     The first name for the billing address. If empty, the <code>customer</code>'s first name will be used
    </details>
    <details>
      <summary> <code>second_name</code> : <code class="type">string</code>
    </summary>
     The second name for the billing address. If empty, the <code>customer</code>'s second name will be used
    </details>
    <details>
      <summary> <code>email</code> : <code class="type">string</code>
    </summary>
     The email for the billing address. If empty, the <code>customer</code>'s email will be used
    </details>
    <details>
      <summary> <code>phone</code> : <code class="type">string</code>
    </summary>
     The phone for the billing address. If empty, the <code>customer</code>'s phone will be used
    </details>
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
     The postal code of the address
    </details>
    <details>
      <summary> <code>city</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The city name
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
     The country code of the country: https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes
    </details>
    <details>
      <summary> <code>country</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The country name of the address.
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
     The <code>line_item</code> total computed as (<code>unit_price</code> - <code>unit_discount_amount</code>) * <code>quantity</code>
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
      <summary><code>non_returnable_reason</code> : <code class="type">int</code>
     </summary>
     The reason behind the item not being returnable: Possible options are:
     <ul>
        <li> <code>2</code> : The item has been already returned</li>
        <li> <code>3</code> : The item is not returnable because of an e-commerce policy</li>
        <li> <code>4</code> : The item (or order) has not been fulfilled yet</li>
        <li> <code>5</code> : The item is out of return period. (REVER already checks this condition internally)</li>
     </ul>
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
      <details>
      <summary> <code>variants</code> : <code class="type">list[object]</code></summary>
      A list of product variants that will be used for product exchanges (please read the section on 1:1 Exchanges for further details)
        <details>
        <summary> <code>variant_id</code> : <code class="type">string</code>
        <code class="required">required</code> </summary>
        The internal id of the variant
        </details>
        <details>
        <summary> <code>variant_name</code> : <code class="type">string</code>
        <code class="required">required</code> </summary>
        The variant name. As before, if this variant is size XL of product "REVER T-Shirt", the variant name should be "XL"
        </details>
        <details >
        <summary> <code>options</code> : <code class="type">list[object]</code>
        <code class="required">required</code> </summary>
        A list of options related with the given variant. This can include color, size, etc.
          <details>
          <summary> <code>option_id</code> : <code class="type">string</code>
          <code class="required">required</code> </summary>
          The internal <code>id</code> of the option
          </details>
          <details>
          <summary> <code>option_name</code> : <code class="type">string</code>
          <code class="required">required</code> </summary>
          The option name. For exaple, if this option was related to the product size, the <code>option_name</code> could be "Size"
          </details>
          <details>
          <summary> <code>option_value</code> : <code class="type">string</code>
          <code class="required">required</code> </summary>
          The value of the option. For example, if the option was related to size, this could be "XL". Or, if it was related with the variant color, then a possible <code>value</code> would be "Green".
          </details>
        </details>
        <details>
          <summary> <code>variant_price</code> : <code class="type">string</code>
          <code class="required">required</code> </summary>
          The variant price in the shop's currency.
        </details>
        <details>
          <summary> <code>variant_description</code> : <code class="type">string</code>
          </summary>
          A variant description that will be shown to the user
        </details>
        <details>
          <summary> <code>variant_sku</code> : <code class="type">string</code>
          </summary>
          The variant sku if available.
        </details>
        <details>
          <summary> <code>is_enabled</code> : <code class="type">boolean</code>
          <code class="required">required</code> </summary>
          A boolean that determines if the shopper should be able to choose this variant for the exchange. You can define the logic behind id. The only requirements for <code>is_enabled : true</code> are that:
          <ul>
          <li>The stock for that variant is greater than 0</li>
          <li>There are no internal restrictions that will not allow REVER to create a order with that given variant </li>
          </ul>
        </details>
      </details>
    </details>

</details>

<details class="detail-object">
    <summary> <code>associated_costs</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    An object with all the costs applied at an order level.
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
    <summary> <code>payment_method</code> : <code class="type">object</code> </summary>
    This object must contain all the information related to the order payment and will allow REVER to offer original payment method (OPM) refunds.
    More details can be found in the OPM refunds section of this document
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
  "order_number": "20045",
  "order_id": "6754",
  "shop_currency": "USD",
  "customer_info": {
    "email": "john.doe@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "phone": 1234567890,
    "currency": "USD"
  },
  "shipping_address": {
    "address_line_1": "1234 Elm Street",
    "address_line_2": "Apt 101",
    "postal_code": "12345",
    "city": "Springfield",
    "state_province": "Ohio",
    "province_code": "OH",
    "country_code": "US",
    "country": "United States"
  },
  "billing_address": {
    "address_line_1": "1234 Elm Street",
    "address_line_2": "Apt 101",
    "postal_code": "12345",
    "city": "Springfield",
    "state_province": "Ohio",
    "province_code": "OH",
    "country_code": "US",
    "country": "United States"
  },
  "line_items": [
    {
      "id": "6754-0-123",
      "variant_name": "XL",
      "variant_id": "123",
      "sku": "TS-001-XL",
      "quantity": 2,
      "unit_price": "19.99",
      "unit_discount_amount": "0.00",
      "total": "39.98",
      "subtotal": "39.98",
      "is_returnable": true,
      "non_returnable_reason": 4,
      "product": {
        "id": "101",
        "name": "Basic White T-Shirt",
        "description": "Classic white t-shirt made from 100% cotton.",
        "images": [
          {
            "src": "https://example.com/images/basic-white-tshirt.jpg",
            "alt": "Basic White T-Shirt back"
          }
        ]
      }
    }
  ],
  "associated_costs": {
    "logistic_costs": "5.00",
    "order_discount_amount": "0.00"
  },
  "payment_method": {
    "platform_id": "platform_id",
    "payment_details": {}
  },
  "exchange_rate": 1.2148596,
  "purchased_at": "2024-03-21T10:15:30-04:00",
  "fulfilled_at": "2024-03-21T10:20:45-04:00"
}
```

#### Error handling

For this enpoint, following the basic http response codes conventions is enough. However, for some errors we will require specific formats:

</details>
<details class="details-object">
<summary><code class="error">404</code></summary>
If the <code>order_number</code> provided in the <code class="get">GET</code> request does not exist in your platform, you should return a <code class="error">404</code> error code.
In the error body try to provide as much information as possible.
</details>

</details>
<details class="details-object">
<summary><code class="server-error">500</code></summary>
In case is your server or platform that is failing (i.e. it's down for expected or unexpected reasons), the response to the REVER <code class="get">GET</code> request should be any <code class="server-error">5XX</code> error code, the one that best applies to the error
</details>

### <code class="get">GET</code> <code> /rever/orders?order_number={order_number} </code>

This will be endpoint that will be used in order to retrieve the information of a given order once the customer starts a return in our portal.

The parameter that will be provided in this `GET` request will be the `order_number`, which is the order number that the customer is given once it's order has been completed / fulfilled by the eCommerce.

The expected fields to be received back from your platform are:

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
    The default currency in your shop. All quantities in the order should be provided in this currency   
</details>

<details class="detail-object">
    <summary> <code>customer_info</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    An object with all the customer information associted with the order
    <details>
      <summary> <code>email</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
      The email that has been used in the order
    </details>
    <details >
      <summary> <code>first_name</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The first name of the customer who has made the purchase
    </details>
    <details >
      <summary> <code>last_name</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The last name of the customer who has made the purchase
    </details>
    <details>
      <summary> <code>phone</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     Phone number of the customer with the correct country code
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
      <summary> <code>first_name</code> : <code class="type">string</code>
    </summary>
     The first name for the shipping address. If empty, the <code>customer</code>'s first name will be used
    </details>
    <details>
      <summary> <code>second_name</code> : <code class="type">string</code>
    </summary>
     The second name for the shipping address. If empty, the <code>customer</code>'s second name will be used
    </details>
    <details>
      <summary> <code>email</code> : <code class="type">string</code>
    </summary>
     The email for the shipping address. If empty, the <code>customer</code>'s email will be used
    </details>
    <details>
      <summary> <code>phone</code> : <code class="type">string</code>
    </summary>
     The phone for the shipping address. If empty, the <code>customer</code>'s phone will be used
    </details>
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
     The postal code of the address
    </details>
    <details>
      <summary> <code>city</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The city name
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
     The country code of the country: https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes
    </details>
    <details>
      <summary> <code>country</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The country name of the address.
    </details>
</details>

<details class="detail-object">
    <summary> <code>billing_address</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    Billing address details
    <details>
      <summary> <code>first_name</code> : <code class="type">string</code>
    </summary>
     The first name for the billing address. If empty, the <code>customer</code>'s first name will be used
    </details>
    <details>
      <summary> <code>second_name</code> : <code class="type">string</code>
    </summary>
     The second name for the billing address. If empty, the <code>customer</code>'s second name will be used
    </details>
    <details>
      <summary> <code>email</code> : <code class="type">string</code>
    </summary>
     The email for the billing address. If empty, the <code>customer</code>'s email will be used
    </details>
    <details>
      <summary> <code>phone</code> : <code class="type">string</code>
    </summary>
     The phone for the billing address. If empty, the <code>customer</code>'s phone will be used
    </details>
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
     The postal code of the address
    </details>
    <details>
      <summary> <code>city</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The city name
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
     The country code of the country: https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes
    </details>
    <details>
      <summary> <code>country</code> : <code class="type">string</code>
     <code class="required">required</code> </summary>
     The country name of the address.
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
     The <code>line_item</code> total computed as (<code>unit_price</code> - <code>unit_discount_amount</code>) * <code>quantity</code>
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
      <summary><code>non_returnable_reason</code> : <code class="type">int</code>
     </summary>
     The reason behind the item not being returnable: Possible options are:
     <ul>
        <li> <code>2</code> : The item has been already returned</li>
        <li> <code>3</code> : The item is not returnable because of an e-commerce policy</li>
        <li> <code>4</code> : The item (or order) has not been fulfilled yet</li>
        <li> <code>5</code> : The item is out of return period. (REVER already checks this condition internally)</li>
     </ul>
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
      <details>
      <summary> <code>variants</code> : <code class="type">list[object]</code></summary>
      A list of product variants that will be used for product exchanges (please read the section on 1:1 Exchanges for further details)
        <details>
        <summary> <code>variant_id</code> : <code class="type">string</code>
        <code class="required">required</code> </summary>
        The internal id of the variant
        </details>
        <details>
        <summary> <code>variant_name</code> : <code class="type">string</code>
        <code class="required">required</code> </summary>
        The variant name. As before, if this variant is size XL of product "REVER T-Shirt", the variant name should be "XL"
        </details>
        <details >
        <summary> <code>options</code> : <code class="type">list[object]</code>
        <code class="required">required</code> </summary>
        A list of options related with the given variant. This can include color, size, etc.
          <details>
          <summary> <code>option_id</code> : <code class="type">string</code>
          <code class="required">required</code> </summary>
          The internal <code>id</code> of the option
          </details>
          <details>
          <summary> <code>option_name</code> : <code class="type">string</code>
          <code class="required">required</code> </summary>
          The option name. For exaple, if this option was related to the product size, the <code>option_name</code> could be "Size"
          </details>
          <details>
          <summary> <code>option_value</code> : <code class="type">string</code>
          <code class="required">required</code> </summary>
          The value of the option. For example, if the option was related to size, this could be "XL". Or, if it was related with the variant color, then a possible <code>value</code> would be "Green".
          </details>
        </details>
        <details>
          <summary> <code>variant_price</code> : <code class="type">string</code>
          <code class="required">required</code> </summary>
          The variant price in the shop's currency.
        </details>
        <details>
          <summary> <code>variant_description</code> : <code class="type">string</code>
          </summary>
          A variant description that will be shown to the user
        </details>
        <details>
          <summary> <code>variant_sku</code> : <code class="type">string</code>
          </summary>
          The variant sku if available.
        </details>
        <details>
          <summary> <code>is_enabled</code> : <code class="type">boolean</code>
          <code class="required">required</code> </summary>
          A boolean that determines if the shopper should be able to choose this variant for the exchange. You can define the logic behind id. The only requirements for <code>is_enabled : true</code> are that:
          <ul>
          <li>The stock for that variant is greater than 0</li>
          <li>There are no internal restrictions that will not allow REVER to create a order with that given variant </li>
          </ul>
        </details>
      </details>
    </details>
</details>

<details class="detail-object">
    <summary> <code>associated_costs</code> : <code class="type">object</code> <code class="required">required</code> </summary>
    An object with all the costs applied at an order level.
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
    <summary> <code>payment_method</code> : <code class="type">object</code> </summary>
    This object must contain all the information related to the order payment and will allow REVER to offer original payment method (OPM) refunds.
    More details can be found in the OPM refunds section of this document
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
  "order_number": "20045",
  "order_id": "6754",
  "shop_currency": "USD",
  "customer_info": {
    "email": "john.doe@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "phone": 1234567890,
    "currency": "USD"
  },
  "shipping_address": {
    "address_line_1": "1234 Elm Street",
    "address_line_2": "Apt 101",
    "postal_code": "12345",
    "city": "Springfield",
    "state_province": "Ohio",
    "province_code": "OH",
    "country_code": "US",
    "country": "United States"
  },
  "billing_address": {
    "address_line_1": "1234 Elm Street",
    "address_line_2": "Apt 101",
    "postal_code": "12345",
    "city": "Springfield",
    "state_province": "Ohio",
    "province_code": "OH",
    "country_code": "US",
    "country": "United States"
  },
  "line_items": [
    {
      "id": "6754-0-123",
      "variant_name": "XL",
      "variant_id": "123",
      "sku": "TS-001-XL",
      "quantity": 2,
      "unit_price": "19.99",
      "unit_discount_amount": "0.00",
      "total": "39.98",
      "subtotal": "39.98",
      "is_returnable": true,
      "non_returnable_reason": 4,
      "product": {
        "id": "101",
        "name": "Basic White T-Shirt",
        "description": "Classic white t-shirt made from 100% cotton.",
        "images": [
          {
            "src": "https://example.com/images/basic-white-tshirt.jpg",
            "alt": "Basic White T-Shirt back"
          }
        ]
      }
    }
  ],
  "associated_costs": {
    "logistic_costs": "5.00",
    "order_discount_amount": "0.00"
  },
  "payment_method": {
    "platform_id": "platform_id",
    "payment_details": {}
  "exchange_rate": 1.2148596,
  "purchased_at": "2024-03-21T10:15:30-04:00",
  "fulfilled_at": "2024-03-21T10:20:45-04:00"
}
```

#### Error handling

For this enpoint, following the basic http response codes conventions is enough. However, for some errors we will require specific formats:

</details>
<details class="details-object">
<summary><code class="error">404</code></summary>
If the <code>order_number</code> provided in the <code class="get">GET</code> request does not exist in your platform, you should return a <code class="error">404</code> error code.
In the error body try to provide as much information as possible.
</details>

</details>
<details class="details-object">
<summary><code class="server-error">500</code></summary>
In case is your server or platform that is failing (i.e. it's down for expected or unexpected reasons), the response to the REVER <code class="get">GET</code> request should be any <code class="server-error">5XX</code> error code, the one that best applies to the error
</details>

### <code class="post">POST</code> <code> /rever/return </code>

This enpoint will be called every time a return has been completed. This is, when the returned object has arrived to your warehouse and been approved by you in the REVER dashboard.

At that point, you will get a <code class="post">POST</code> request to this endpoint with all the information regarding the return process that has been completed so you can reflect it in you platform.

The <code>body</code> that will be inclued in this request to your endpoint is:

<details class="detail-object">
    <summary> <code>ecommerceID</code> : <code class="type">string</code></summary>
    A string identifier that we use to identify your platform inside our returns portal and dashboard
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
      The value  of the return without taking into account the shipping costs. The amount is sent without decimals, so 26.35€ would be sent as 2635.
      </details>
      <details>
      <summary> <code>amount_after_costs</code> : <code class="type">int</code></summary>
      The value of the return with shipping costs apportioned between all items. The amount is sent without decimals, so 26.35€ would be sent as 2635.
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
    The refunded amount from the original shipping costs. The amount is sent without decimals, so 4.96€ would be sent as 496.
</details>
<details class="detail-object">
    <summary> <code>return_shipping_cost_amount</code> : <code class="type">int</code></summary>
    The amount paid by the customer (or subtracted from the total refund amount) for the return shipping costs. The amount is sent without decimals, so 10.00€ would be sent as 1000.
</details>

#### Example

The following is an example of how a REVER return will look like:

```json
{
  "ecommerceID": "example",
  "orderID": "3924",
  "currency": "EUR",
  "return_line_items": [
    {
      "line_item_id": "1654",
      "quantity": 1,
      "amount_before_costs": 2635,
      "amount_after_costs": 2158,
      "return_status": "APPROVED"
    }
  ],
  "shipping_cost_refund_amount": 496,
  "return_shipping_cost_amount": 1000
}
```

#### Response

The response provided by this endpoint after the <code class="post">POST</code> request should be:

<details class="detail-object">
<summary><code class="ok">200</code></summary>
For successful <code class="post">POST</code> requests we should get back a <code>200</code> status code with the following body:<pre><code>
{
	"success": true
}
</code></pre>
</details>
<details class="detail-object">
<summary><code class="error">406</code></summary>
For unsuccessful <code class="post">POST</code> requests where the data was invalid we should get back a <code class="error">406</code> status code with the following body:
<pre><code>
{
	"error": "Invalid data"
}
</code></pre>
</details>

## Promocodes

Promocodes allow users to get a gift card with the same value of the returned items instead of a refund. This feture is beneficial for you since you ensure that the customer will spend the money in your store. For promocodes to be active, REVER needs to have access to a new endpoint in your platform, which will receive the promocode info.

### <code class="post">POST</code> <code>/rever/promocode</code>

This will be the endpoint where you will receive the promocode details. The body of this <code class="post">POST</code> request will be:

<details class="detail-object">
<summary> <code>ecommerceID</code> : <code class="type">string</code></summary>
The eCommerce's unique identifier
</details>

<details class="detail-object">
<summary> <code>promocode</code> : <code class="type">string</code></summary>
The code for the promocode. It will follow the following format <code>RV-{order_number}-{random_string}</code> and its length will always be 15 characters.
</details>

<details class="detail-object">
<summary> <code>total_shop</code> : <code class="type">float</code></summary>
The total value of the promocode in the shop's currency
</details>

<details class="detail-object">
<summary> <code>currency_shop</code> : <code class="type">string</code></summary>
The currency associated to the shop
</details>

<details class="detail-object">
<summary> <code>total_customer</code> : <code class="type">float</code></summary>
The total value of the promocode in the currency that was used by the customer
</details>

<details class="detail-object">
<summary> <code>currency_shop</code> : <code class="type">string</code></summary>
The code of the currency that was used by the user
</details>

#### Example

Here is an example of the JSON body:

```json
{
  "ecommerceID": "yourEcommerceID",
  "order_number": "123456",
  "promocode": "RV-123456-kasdf",
  "total_shop": 56.44,
  "currency_shop": "EUR",
  "total_customer": 25.36,
  "currency_customer": "USD"
}
```

#### Error handling

Error handling for this endpoint will be quite simple:

<details class="detail-object">
  <summary><code class="ok">200</code></summary>
  For successful <code class="post">POST</code> requests we expect a <code class="ok">200</code> status code. 
</details>

<details class="detail-object">
  <summary><code class="error">4XX</code></summary>
  For errors related with <code class="post">POST</code> request we expect a <code>4XX</code> status code that matches the error. However, for some errors we require a specifict error code:
  <details>
    <summary><code class="error">404</code> - Not found</summary>
    If you couldn't identify the order in your system
  </details>
  <details>
    <summary><code class="error">400</code> - Bad request</summary>
    If you detect that the body received in the <code class="post">POST</code> request does not follow the format that has been specified above or has some information missing, you should return a <code class="error">400</code> status code.
  </details>
</details>

<details class="detail-object">
  <summary><code class="server-error">500</code></summary>
  In case is your server or platform that is failing (i.e. it's down for expected or unexpected reasons), the response to the REVER <code class="post">POST</code> request should be any <code class="server-error">5XX</code> error code, the one that best applies to the error
</details>

## OPM refunds

When a customer starts a return with REVER, various refund methods are available to choose from:

- Bank transfer
- Gift Card
- OPM (Original Payment Method)

When a user selects OPM, he/she will receive the refunded money to the same card or payment method that was used when paying for the order. This means that, if a customer has paid for the order with a given credit card, the return amount will be refunded to that same card.

To be able to offer OPM to your customers, the following object must be added to the JSON body returned by the <code class="get">GET</code> <code> /rever/order</code> endpoint:

<details class="detail-object">
<summary> <code>payment_method</code> : <code class="type">object</code></summary>
Object containing the payment details for the order
<details>
<summary> <code>platform_id</code> : <code class="type">string</code></summary>
An identifier of the payment platform that have been used for the order. Supported platforms so far are:
- Paypal
- Redsys
Integrations with additional platforms can be requested and REVER will work on developing the integration
</details>
<details>
<summary> <code>payment_details</code> : <code class="type">object</code></summary>
An object with the necessary details of the payment in order for REVER to be able to generate the refund. This will vary based on the platform. Please see next section in the documentation.
</details>
</details>

### Paypal - <code>payment_details</code> object

For a Paypal refund, this must be the structure of the <code>payment_details</code> object:

<details class="detail-object">
<summary> <code>payment_details</code> : <code class="type">object</code></summary>
Object containing the necessary details of the Paypal refund.
<details>
<summary> <code>transaction_id</code> : <code class="type">string</code><code class="required">required</code></summary>
The transaction ID provided by Paypal as <code>capture_id</code> for the given payment.
</details>
</details>

#### Example

The following is an example of how <code>payment_method</code> JSON object would look like for payal:

```json
"payment_method": {
    "platform_id": "paypal",
    "payment_details": {
      "transaction_id": "123456789"
    }
  }
```

### Redsys - <code>payment_details</code> object

For a Redsys refund, this must be the structure of the <code>payment_details</code> object:

<details class="detail-object">
<summary> <code>payment_details</code> : <code class="type">object</code></summary>
Object containing the necessary details of the Redsys refund.
<details>
<summary> <code>transaction_id</code> : <code class="type">string</code><code class="required">required</code></summary>
The order provided by Redsys as <code>DS_MERCHANT_ORDER</code> for the given payment.
</details>
</details>

#### Example

The following is an example of how <code>payment_method</code> JSON object would look like for Redsys:

```json
"payment_method": {
    "platform_id": "redsys",
    "payment_details": {
      "transaction_id": "123456789"
    }
  }
```

## Variant exchanges

### The <code>variant</code> object

The following is the <code>variants</code> list that was defined above. It must be included in <code>line_items</code> > <code>product</code> for endpoints <code class="get">GET</code> <code>/rever/orders/{order_id}</code> and <code class="get">GET</code> <code>/rever/orders?order_number={order_number}</code>:

<details class="detail-object">
  <summary> <code>variants</code> : <code class="type">list[object]</code></summary>
  A list of product variants that will be used for product exchanges (please read the section on 1:1 Exchanges for further details)
    <details>
    <summary> <code>variant_id</code> : <code class="type">string</code>
    <code class="required">required</code> </summary>
    The internal id of the variant
    </details>
    <details>
    <summary> <code>variant_name</code> : <code class="type">string</code>
    <code class="required">required</code> </summary>
    The variant name. As before, if this variant is size XL of product "REVER T-Shirt", the variant name should be "XL"
    </details>
    <details>
    <summary> <code>options</code> : <code class="type">list[object]</code>
    <code class="required">required</code> </summary>
    A list of options related with the given variant. This can include color, size, etc or it can be empty if the variant has no options.
      <details>
      <summary> <code>option_id</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
      The internal <code>id</code> of the option
      </details>
      <details>
      <summary> <code>option_name</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
      The option name. For exaple, if this option was related to the product size, the <code>option_name</code> could be "Size"
      </details>
      <details>
      <summary> <code>option_value</code> : <code class="type">string</code>
      <code class="required">required</code> </summary>
      The value of the option. For example, if the option was related to size, this could be "XL". Or, if it was related with the variant color, then a possible <code>value</code> would be "Green".
      </details>
    </details>
    <details>
      <summary> <code>variant_price</code> : <code class="type">string</code>
    </summary>
      The variant price in the shop's currency. This field might not be neeeded since the order generated since this variant exchange will have the same price as the original item.
    </details>
    <details>
      <summary> <code>variant_description</code> : <code class="type">string</code>
      </summary>
      A variant description that will be shown to the user
    </details>
    <details>
      <summary> <code>variant_sku</code> : <code class="type">string</code>
      </summary>
      The variant sku if available.
    </details>
    <details>
      <summary> <code>is_enabled</code> : <code class="type">boolean</code>
      <code class="required">required</code> </summary>
      A boolean that determines if the shopper should be able to choose this variant for the exchange. You can define the logic behind id. The only requirements for <code>is_enabled : true</code> are that:
      <ul>
      <li>The stock for that variant is greater than 0</li>
      <li>There are no internal restrictions that will not allow REVER to create a order with that given variant </li>
      </ul>
    </details>
  </details>

In this section more details and edge cases about the <code>variant</code> object will progressively be added.

### <code class="post">POST</code> <code>/rever/orders</code>

When a customer chooses to exchange their original product by another variant and completes the process, a <code class="post">POST</code> request will be sent to this endpoint containing all the necessary information about the new order. This will be the body structure:

<details class="detail-object">
  <summary> <code>ecommerceID</code> : <code class="type">string</code></summary>
  The eCommerce's unique identifier
</details>
<details class="detail-object">
  <summary> <code>order</code> : <code class="type">object</code></summary>
  All the information for the new order
  <details>
    <summary> <code>customer_info</code> : <code class="type">string</code></summary>
    The customer's information
    <details>
      <summary> <code>first_name</code> : <code class="type">string</code></summary>
      Customer's first name
    </details>
    <details>
      <summary> <code>last_name</code> : <code class="type">string</code></summary>
      Customer's last name
    </details>
    <details>
      <summary> <code>email</code> : <code class="type">string</code></summary>
      Customer's email
    </details>
    <details>
      <summary> <code>phone</code> : <code class="type">string</code></summary>
      Customer's phone number
    </details>
    <details>
      <summary> <code>company</code> : <code class="type">string</code></summary>
      The customer's company if provided.
    </details>
    <details>
      <summary> <code>curreny</code> : <code class="type">string</code></summary>
      The currency used by the user
    </details>
  </details>
  <details>
    <summary> <code>line_items</code> : <code class="type">list[object]</code></summary>
    A list of all the items included in the new order
    <details>
      <summary> <code>variantID</code> : <code class="type">string</code></summary>
      The variant ID for the new purchased item
    </details>
    <details>
      <summary> <code>productID</code> : <code class="type">string</code></summary>
      The product ID for the new purchased item
    </details>
    <details>
      <summary> <code>quantity</code> : <code class="type">number</code></summary>
      The quantity of items purchased
    </details>
  </details>
  <details>
    <summary> <code>shipping_address</code> : <code class="type">string</code></summary>
    Shipping address details for the new order
    <details>
      <summary> <code>addreess_line_1</code> : <code class="type">string</code></summary>
      The first address line
    </details>
    <details>
      <summary> <code>addreess_line_2</code> : <code class="type">string</code></summary>
      The second address line. It might be an empty string
    </details>
    <details>
      <summary> <code>city</code> : <code class="type">string</code></summary>
      The city name
    </details>
    <details>
      <summary> <code>state_province</code> : <code class="type">string</code></summary>
      The state or province name
    </details>
    <details>
      <summary> <code>state_province_code</code> : <code class="type">string</code></summary>
      The state or province code
    </details>
    <details>
      <summary> <code>postcode</code> : <code class="type">string</code></summary>
      The postcode number
    </details>
    <details>
      <summary> <code>country</code> : <code class="type">string</code></summary>
      The coutry name
    </details>
    <details>
      <summary> <code>country_code</code> : <code class="type">string</code></summary>
      The country code
    </details>
  </details>
  <details>
    <summary> <code>payment</code> : <code class="type">string</code></summary>
    In case the price of the variant the user has chosen is higher than the original one, the customer will be charged for the difference. This field will contain the details about the payment.
    <details>
      <summary> <code>amount</code> : <code class="type">number</code></summary>
      Amount paid by the customer for the exchange
    </details>
    <details>
      <summary> <code>currency</code> : <code class="type">string</code></summary>
      Currency in which the payment was made
    </details>
    
  </details>

</details>

<details class="detail-object">
  <summary> <code>original_order</code> : <code class="type">string</code></summary>
  Information about the original order
  <details>
    <summary> <code>orderID</code> : <code class="type">string</code></summary>
    The ID of the original order
  </details>
  <details>
    <summary> <code>order_number</code> : <code class="type">string</code></summary>
    The order number of the original order
  </details>

  <details>
    <summary> <code>total</code> : <code class="type">float</code></summary>
    The total payed by the customer for the original order
  </details>
  <details>
    <summary> <code>currency</code> : <code class="type">string</code></summary>
    The currency used by the customer in the original order
  </details>

</details>

#### Example

```json
{
  "ecommerceID": "yourEcommerceID",
  "order": {
    "customer_info": {
      "first_name": "John",
      "last_name": "Doe",
      "email": "john.doe@example.com",
      "phone": "+34654252928",
      "company": "Example Company",
      "currency": "EUR"
    },
    "line_items": [
      {
        "variantID": "variant_id_1",
        "productID": "product_id_1",
        "quantity": 2
      },
      {
        "variantID": "variant_id_2",
        "productID": "product_id_2",
        "quantity": 1
      }
    ],
    "shipping_address": {
      "address_line_1": "123 Shipping St",
      "address_line_2": "Apt 101",
      "city": "Shipping City",
      "state_province": "Shipping State",
      "postcode": "12345",
      "country": "Shipping Country",
      "country_code": "SC",
      "state_province_code": "SS"
    },
    "payment": {
      "amount": 10.0,
      "currency": "EUR"
    }
  },
  "original_order": {
    "orderID": "example_original_order_id",
    "order_number": "example_original_order_number",
    "total": 52.74,
    "currency": "EUR"
  }
}
```

#### Response body

For successful <code class="post">POST</code> requests we expect a <code class="ok">200</code> status code. Moreover, in the response body we should get:

<details class="detail-object">
    <summary> <code>order_id</code> : <code class="type">string</code> <code class="required">required</code>
    </summary>
    The internal id of the new order
  </details>
  <details class="detail-object">
    <summary> <code>order_number</code> : <code class="type">string</code> <code class="required">required</code>
    </summary>
    The order number (i.e.: the one given to the client for him/her to be able to identify his order) of the new order
  </details>
    <details class="detail-object">
    <summary> <code>order_total_price</code> : <code class="type">number</code> <code class="required">required</code>
    </summary>
    The total price of the new order in your platform
  </details>
  <details class="detail-object">
    <summary> <code>currency</code> : <code class="type">string</code> <code class="required">required</code>
    </summary>
    The currency code of the new order
  </details>
  <details class="detail-object">
    <summary> <code>line_items</code> : <code class="type">list[object]</code> <code class="required">required</code> 
    </summary>
    A list of all the line_items included in the new order. Ideally, they would be the same ones you have received in the <code class="post">POST</code> request, but in some cases you might not be able to fulfill some of the items due to stock issues or other reasons. In that case, you should return the <code>line_items</code> that you were able to fulfill.
    <details>
        <summary> <code>quantity</code> : <code class="type">number</code> <code class="required">required</code>
        </summary>
        The quantity of the given line item
    </details>
    <details>
        <summary> <code>variant_id</code> : <code class="type">string</code> <code class="required">required</code>
        </summary>
        The internal id of the variant
    </details>
    <details>
        <summary> <code>product_id</code> : <code class="type">string</code> <code class="required">required</code>
        </summary>
        The internal id of the product
    </details>
  </details>

This is an example of a body we could get back from this endpoint:

```json
{
  "order_id": "12345",
  "order_number": "RV567",
  "order_total_price": 25.16,
  "currency": "EUR",
  "line_items": [
    {
      "quantity": 1,
      "variant_id": "16761",
      "product_id": "487315"
    },
    {
      "quantity": 3,
      "variant_id": "47623",
      "product_id": "572937"
    }
  ]
}
```

#### Error handling

For unsuccessful requests we expect:

<details class="detail-object">
  <summary><code class="error">4XX</code></summary>
  For errors related with <code class="post">POST</code> requests we expect a <code>4XX</code> status code that matches the error. However, for some errors we require a specifict error code:
  <details>
    <summary><code class="error">409</code> - Conflict</summary>
    In case the order could not be created for any reason such as (but not limited to):
    <ul>
      <li>There is no stock for the given variant</li>
      <li>The product is no longer available in the store</li>
    </ul>
    Some errors that are excluded from this case are:
    <ul>
      <li>Bad request</li>
      <li>You were not able to determine the correct <code>line_items</code> for the order</li>
    </ul>
  </details>
  <details>
    <summary><code class="error">400</code> - Bad request</summary>
    If you detect that the body received in the <code class="post">POST</code> request does not follow the format that has been specified above or has some information missing, you should return a <code class="error">400</code> status code.
  </details>
</details>
