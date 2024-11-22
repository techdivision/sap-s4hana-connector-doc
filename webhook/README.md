# Order history webhook

The order history webhook is intended to return a history of orders to the frontend. Ideally a headleass frontend will
call this webhook to retrieve orders. It does this by handing over the customer token which was created during login.

## Generate customer token

On the frontend side the customer token is generated like this:

```bash
curl --request POST \
--url https://COMMERCE-BASE-URL/rest/V1/integration/customer/token \
--header 'Content-Type: application/json' \
--data '{
"username": "user",
"password": "password"
}'
```

## Call the webhook

The webhook is secured via Adobe IMS. Therefore you need a valid OAuth 2 token which you can generate via the token
endpoint:

```bash
curl -X POST 'https://ims-na1.adobelogin.com/ims/token/v3' -H 'Content-Type: application/x-www-form-urlencoded' -d 'grant_type=client_credentials&client_id=XXX&client_secret=XXX&scope=AdobeID,openid,read_organizations,additional_info.projectedProductContext,additional_info.roles,adobeio_api,read_client_secret,manage_client_secrets,event_receiver_api'
```

Besides the token you also need to pass the Adobe IMS org id as header parameter `x-gw-ims-org-id`.

```bash
curl --request POST \
  --url https://IO-RUNTIME-BASE-URL/api/v1/web/webhook/order-history \
  --header 'Authorization: Bearer ACCESS_TOKEN' \
  --header 'Content-Type: application/json' \
  --header 'x-gw-ims-org-id: ADOBE_IMS_ORG_ID' \
  --data '{
	"token": "COMMERCE_CUSTOMER_TOKEN",
	"top": 2,
	"skip": 4
}'
```

You can paginate through the orders with the parameters `top` and `skip`.
