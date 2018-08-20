# hyper-business

## Installation

take this from the repo
then in your shell:
`$ hyper-business-installer`

## Usage
add the business operation class to you config as valid, for security:
`Hyperstack.valid_business_class_names = ['earn_money']`

params are used exactly like hyper-react params.

Example Operation:
```ruby
class EarnMoney < Hyperloop::Business
  param :user_id
  param :products
  param :cc_number

  # business speak here:
  First 'Make sure we have a valid user.'
  Then 'Check availability of ordered products.'
  Then 'Get users credit card number.'
  Finally 'Execute order.'

  # coder speak here:
  code_for 'Make sure we have a valid user.' do
    user = Member.find(params.user_id)
    if user && user.id && user.email != 'joe@evil.com'
      :user_valid
    else
      raise 'Invalid user!'
    end
  end

  code_for 'Check availability of ordered products.' do
    params.products.each do |product|
      if product
        :ok
      else
        raise 'No such product'
      end
    end
  end

  code_for 'Get users credit card number.' do
    if params.cc_number
      :ok
    else
      raise 'Scammer alarm!'
    end
  end

  code_for 'Execute order.' do
    # something useful here
    :final_result
  end
end
```
There also is a `Failed 'Description'` and a `code_for_failed 'Description' { block code here }`.
There also is a `OneStep 'description` use with `code_for`.
`First, Then, Finally, OneStep, Failed` all also accept a block.

Execute like:
```ruby
params = { user_id: 1, products: [], cc_number: '123456' }
EarnMoney.run(params).then { |result| puts result } # local run, result is returned
EarnMoney.run_on_server(params).then { |result| puts result } # result should be returned, but currently is not
EarnMoney.run_on_client(session_id, params) # run for side effects, result is currently not returned
```
