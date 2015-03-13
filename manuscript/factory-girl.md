# Factory Girl

One of the big challenges in setting up tests is how to efficiently set up the context of the test. It's not uncommon to see complicated object graphs set up prior to the test being run.

```ruby
RSpec.describe "Shopping Cart" do
  let(:user) { User.create(username: "DVG", password: "secret",
                           password_confirmation: "secret") }
  let(:past_order) { Order.create(user: user) }
  let(:playstation) { Product.create(name: "Playstation 4", price: "299.99", category: category) }
  let(:game) { Product.create(name: "Borderlands Handsome Collection", price: "59.99",
                              category: category) }
  let(:expected_recommendation) { Product.create(name: "Dragon Age: Origins", price: "39.99",
                                  category: category) }
  let(:category) { Category.create(name: "Video Games and Electronics") }
  before { user.past_orders << past_order }
  subject { Cart.create(user: user) }
  it "Recommends Products from the users favorite category" do
    # The test!
  end
end
```

So we want a user that has ordered things in the past from a category to get a recommendation from that category when they buy something else from that category. We end up building a bunch of objects to establish that context. While creating objects by hand is a fine thing to do most of the time, test data builders can clean up our test code a lot by letting us create these graphs elsewhere in a re-uasable way. The de-facto standard for this is FactoryGirl.
