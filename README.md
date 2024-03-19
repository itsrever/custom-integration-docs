# Custom integrations with REVER
The REVER portal can be integrated with any custom eCommerce platform with very little work required from the eCommerce side.

In order to integrate with you custom platform, REVER only needs to have access to a few endpoints that will be defined and described in this file.

These will be classfied into a few sections:
- Compulsory: those endpoints that must be implemented in order for the portal to be functional
- Optional: these endpoints will be classified by functionality.

## Compulsory
These are the required enpoints to be able to integrate our returs portal with your platform
### Get orders (`GET /rever/orders/{order_number}`)
This will be endpoint that will be used in order to retrieve the information of a given order once the customer starts a return in our portal.

The parameter that will be provided in this `GET` request will be the `order_number`, which is the order number that the customer is given once it's order has been completed / fulfilled by the eCommerce.

The expected fields to be received back from your platform are:
- Order number: the order identifier given to the customer (`order_number`: `string`)
- Order id: the unique internal order identifier (`order_id`: `string`)
- Shop currency: the default currency in your shop (`shop_currency`: `string`)
  - All quantities in the order should be provided in this currency
- Customer information: this will be a JSON object containing all the customer's details (`customer_info`)
  - Customer email: the email that has been used in the order (`email`: `string`)
  - First name (`first_name`: `string`)
  - Last name (`last_name`: `string`)
  - Phone (`phone` : `int`)
  - Customer currency: the currency that ha been used by the customer when purchasing (`currency`)
    - In our returns portal, quantities will be shown in this currency.
- Shipping address details (`shipping_address`)
  - Address line 1: this should be, either, the complete address or the address information up to the house number (`address_line_1`)
  - Address line 2: this field should contain the address information regarding flat number, door number, etc or be empty (`address_line_2`)
  - Postal code (`postal_code`: `int`)
  - City (`city`: `string`)
  - Province / state name (`state_province`: `string`)
  - Province code: a code that identifies the province (`province_code`: `string`)
    - For spanish provinces: https://es.wikipedia.org/wiki/Anexo:Provincias_de_Espa%C3%B1a_por_c%C3%B3digo_postal
  - Country code (`country_code`: `string`)
  - Country name (`country`: `string`)
- Billing address details (`billing_address`)
  - Address line 1: this should be, either, the complete address or the address information up to the house number (`address_line_1`)
  - Address line 2: this field should contain the address information regarding flat number, door number, etc or be empty (`address_line_2`)
  - Postal code (`postal_code`: `int`)
  - City (`city`: `string`)
  - Province / state name (`state_province`: `string`)
  - Province code: a code that identifies the province (`province_code`: `string`)
    - For spanish provinces: https://es.wikipedia.org/wiki/Anexo:Provincias_de_Espa%C3%B1a_por_c%C3%B3digo_postal
  - Country code (`country_code`: `string`)
  - Country name (`country`: `string`)
- Line items: a list of all the items purchased in the order (`line_items`)
  - Line item ID: this should be a unique ID that, when provided, allows you to identificate the specific line items from all line items and orders (`id`: `string`)
  - Variant name (`variant_name`: `string`)
    - For example, if the customer has bought "REVER T-Shirt XXS" and the product is "REVER T-Shirt", then the variant name should be "XXS"
  - Variant ID: unique variant identifier (`variant_id`: `string`)
  - SKU of the product, if available (`sku`: `string`)
  - Quantity of products purchased (`quantity`: `int`)
  - Unit price: the price of a single unit of the given product (`unit_price`: `string`)
  - Discount amount applied to each unit (`unit_discount_amount`: `string`)
  - Total: the `line_item` total computed as `unit_price` * `quantity` (`total`: `string`)
  - Subtotal: the `line_item` subtotal computed as (`unit_price` - taxes) * `quantity` (`subtotal`: `string`)
  - Is the product returnable (`is_returnable`: `boolean`)
    - Some products might not be returnable due to its nature or because they have been customized. In that cased, `is_returnable` should be `false`.
      - If `"is_returnable" : false`, all the products in the given line item will be marked as non-returnable.
  - Product: a JSON object with details of the parent product (`product`)
    - Product ID (`id`: `string`)
    - Product name (`name`: `string`)
      - Following with the previous example of a `line_item` for "REVER T-Shirt XXS", the product name should be "REVER T-Shirt".
    - Product description (`description`: `string`)
    - Images: a list of product images (one is enough) (`images`)
      - Image URL (`src`: `string`)
      - Alt text for the image (`alt`: `string`)
  - Associated costs (`associated_costs`)
    - Logistics costs: amount paid by the user for logistics (`logistic_costs`: `string`)
    - Total amount of discounts applied (`order_discount_amount`)
  - Purchased at date in format AAAA-MM-DDThh:mm:ss+zz:zz (ISO 8601) (`purchased_at`: `string`)
  - Fulfilled at date in format AAAA-MM-DDThh:mm:ss+zz:zz (ISO 8601) (`fulfilled_at`: `string`)

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


### Complete return (`POST /rever/return`)
