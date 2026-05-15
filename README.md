# API

A server offering many APIs needed for agentic operations, specifically on the ListenUp VOIS ecosystem.

## Usage

This server uses ETHAuth and Open402 specifications for access and payment (respectively). The flow is:

1) submit your Ethereum Address and an acceptable Chain ID to: [GET] `/challenge` `?` `address=` & `chainId=`
2) the endpoint returns a `nonce` value in JSON that must be signed with the same credentials
3) submit your signed message to [POST] `/login` in a JSON object as `signature` along with the unsigned `nonce`, original `address` and `chainId`
4) the endpoint returns a time sensetive `token` value in JSON if the signature is valid
6) until expiration (typically 15 minutes) you may use the JWT as `Bearer {token}` in `Authorization` header via [POST] to any of the API endpoints for authorization
7) at this point the merchant may validate that the `Address` exists on the `chainId`... holds some token... in some amount

The flow above allows you to prove you own the account in question and can make payment if required. Payments may be made on a per-request basis or deducted from an allocation, depending on the api.

### To make a one time payment:
8) Submit a proper request to the API endpoint with the JWT as `Bearer {token}` in `Authorization` header via [POST].
9) If your request is missing anything, an error should specify the issue; otherwise, a request for payment will be issued in JSON (with `payTo`/`chainId`/`cost`/`jobId`/`expiresIn` values) along with a request `statusUrl` to track progress.
10) You must make the payment in the exact `cost` amount, to the exact `payTo` address, on the proper `chainId` and pay the gas fees.
11) The system will periodically check if payment has been made (from specified wallet in specified amount in specified token on specified chain) until the expiration time. If payment is detected, the `status` will change to `paid` and a `txHash` will be returned including the url where you can find the completed work.

## TODO:
### To prepay in advance:
12) If the fee is a known or fixed `cost` to a known `payTo` address on a known `chainId`, you may pre-pay it and include the `txHash` value in the original request as confirmation of prepayment. The transaction must be in the latest block or an immediate predecessor.

### To utilize a credit allocation:
1X) 

### If payment is not detected:
13) You may include the `txHash` value in a [POST] request to the `statusUrl` as confirmation of payment. The transaction must be in the latest block or an immediate predecessor and match the exact `payTo` address, on the proper `chainId`. If the `cost` amount is greater than expected or from a different `address` than expected, the job may still complete with some delay.

### If an error in payment was made:
14) If the job is not complete, a credit request can be submitted by [POST] to the `statusUrl` specifying the `txHash`, `creditRequestReason` and `contactEmailAddress`. This will queue a support ticket which will eventually get review.
