## Lipa na Mpesa Online

Lipa na Mpesa Online(STK Push) is a Customer to Business Payment. 

When a `customer` clicks a button to make payment, a prompt will be sent to his/her phone requesting for an Mpesa pin to authorize and complete a payment.

Lipa na Mpesa Online transaction [flow](https://developer.safaricom.co.ke/Documentation),

- The Merchant(Partner) captures and sets the API required parameters and sends the API REQUEST to the endpoint: https://sandbox.safaricom.co.ke/mpesa/stkpush/v1/processrequest
- The API receives and token Authentication and API access Authorization, then sends you an acknowledgement RESPONSE back to the Merchant (Partner).
- Through API Proxy  an STK Push trigger request is sent to the M-PESA registered phone number of the customer's making the payment.
- The customer confirms by entering their M-PESA PIN.
- The response is sent back to M-PESA and is processed as below:
- M-PESA Validates the customers PIN
- M-PESA Debits the customer's Mobile Wallet.
- M-PESA credits the Merchant (Partner) account.
- Once request is processed sends the RESULTS back to the API Management system which is then forwarded   to the merchant via the callback URL specified in the REQUEST. 
- The customer receives a SMS confirmation message of the payment.


## Authorization

Gives you an access token to call allowed APIs.

We are going to use a simple ruby script, 

Create a file `access_token.rb`

```rb
require 'base64'
require 'json'
require 'httparty'

module Mpesa
  class AccessToken
    class << self
      def call(key = nil, secret = nil)
        new(key, secret).get_new_access_token
      end
    end

    def initialize(key = nil, secret = nil)
      @key = key.nil? ? "your_api_key" : key
      @secret = secret.nil? ? "your secret_key" : secret
    end

    def get_new_access_token
      response = HTTParty.get(url, headers: headers)

      hash = JSON.parse(response.body).fetch('access_token')
    end

    private

    def url
      "#{"https://sandbox.safaricom.co.ke"}#{"/oauth/v1/generate?grant_type=client_credentials"}"
    end

    def headers
      encode = encode_credentials @key, @secret
      {
        "Authorization" => "Basic #{encode}"
      }
    end

    def encode_credentials key, secret
      credentials = "#{key}:#{secret}"
      Base64.encode64(credentials).split("\n").join
    end
  end
end

obj = Mpesa::AccessToken.call

puts obj

```
Remember to add your `api` and `secret` keys, after running this script an access token will be prinited out, 

Check full implemtation in [this branch](https://github.com/Rails-is-Underrated/Duka/pull/12/files).

***

See you in the next section 👉

