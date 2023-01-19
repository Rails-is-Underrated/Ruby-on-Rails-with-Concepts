## Paystack API

Paystack popup provides a simple an convinient payment flow for the [web](https://paystack.com/docs/payments/accept-payments)

Read through their [documentation](https://paystack.com/docs/payments/accept-payments) for more details.

Let's promt the popup with a small ruby script, 

Ensure you have the right credentials, 

Create a file `touch initialize.rb`
```rb
require 'json'
require 'httparty'

class Initialize 
  class << self
    def transaction
      response = HTTParty.post( url, headers: headers, body: body)

      JSON.parse(response.body)
    end

    private

    def url
      "https://api.paystack.co/transaction/initialize"
    end

    SECRET_KEY = "valid secret key"

    def headers
      {
        Authorization: 'Bearer ' + SECRET_KEY,
        'Content-Type': 'application/json'
      }.freeze
    end

    def body 
      {
        email: "valid_email", 
        amount: 300
      }.to_json
    end
  end
end

response = Initialize.transaction

puts response

```

This will initialize the paystack API and return a hash.

Check out [this branch](https://github.com/Rails-is-Underrated/Duka/pull/14/files) for a complete implementation.

Thank you for reading to the end.

DM me for link to the project and repository @ **simonnjunu16@gmail.com**
