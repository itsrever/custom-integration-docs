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


### Complete return (`POST /rever/return`)
