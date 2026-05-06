# API

A server offering many APIs needed for agentic operations, specifically on the ListenUp VOIS ecosystem.

## Usage

This server uses ETHAuth and Open402 specifications for access and payment (respectively). The flow is:

1) submit your Ethereum Public Key (and optionally chain ID) to `[any API endpoint]/challenge` [GET]`?` `api=` `&chainid=`
2) if your key is valid, the endpoint responds with a nonce value that you will sign with the private key for the aformentioned public key.
3) submit your signed message to `[any API endpoint]/login` [POST] as `signature`
4) if the signature is valid (and optionally: exists on network; holds token; in some amount;) then a JWT will be issued
5) until expiration (typically 15 minutes) you may use the JWT as Bearer token in `[any API endpoint]/resource` [POST] as Authorization

The flow above allows you to prove you own the account in question and can make payment if required. Payments may be made on a per-request basis or deducted from an allocation, depending on the api.

### To make a one time payment:
6) Submit a proper request to the API endpoint.
7) If your request is missing anything, an error should specify the issue; otherwise, a request for payment may be specified (with wallet/network/amount data), and a request status URL should be specified. If the fee is a fixed amount, you may include a tx identifier as confirmation of prepayment, but only once.
8) Until payment is made, the request status will reply with a "Pending Payment" message.
9) Once payment is detected, the request status will reply with the full response.
