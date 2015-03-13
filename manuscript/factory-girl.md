# Factory Girl

One of the big challenges in setting up tests is how to efficiently set up the context of the test. It's not uncommon to see complicated object graphs set up prior to the test being run.

```ruby
RSpec.describe "Shopping Cart" do
  let(:user) { User.create(username: "DVG", 
                           email: "dvg@example.com",
                           password: "secret",
                           password_confirmation: "secret") }
  let(:past_order) { Order.create(user: user) }
  let(:playstation) { Product.create(name: "Playstation 4",
                                     price: "299.99",
                                     category: category) }
  let(:game) { Product.create(name: "Borderlands Handsome Collection",
                              price: "59.99",
                              category: category) }
  let(:expected_recommendation) { Product.create(name: "Dragon Age: Origins",
                                  price: "39.99",
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

## Getting Started

After installing the `factory_girl` gem, you need to define your desired factories. The default location for factories is `spec/factories`, though it's easy to override this if you want to use a different location.

Normally, you'll have one factory file for each ActiveRecord model you have. It's perfectly possible to just have one big factories.rb file with everything in it, but I would suggest you don't do that. If you use a Rails preloader like zeus to speed up test execution times, it has to re-evaluate the factory file after each change, and it will go much faster re-evaluating a small file than a giant one.

### Basic Factory

Okay, now lets define a basic factory.

```ruby
FactoryGirl.define do
  factory :user do
    username "DVG"
    email "dvg@example.com"
    password "secret"
    password_confirmation "secret"
  end
end
```

This is the default implementation of user and will define the required fields as declared by the user model. If you call `FactoryGirl.create(:user)` in a test, it will yield a user record with these attributes without having to specify them over again. If you want to change a specific attribute, you can just pass it in with the call to create.

```
let(:user) { FactoryGirl.create :user, username: "TonyStark" }
```

### Dynamic Attributes

There are going to be times you want to have an attribute that depends on other attributes in the factory, or on other factors altogether. For instance, in the user factory we have manually specified `password` and `password_confirmation`. In the current implementation, if we want to change what the user's password is, we'll have to give both. We can, however, derive the confirmation from the password like so:

```ruby
FactoryGirl.define do
  factory :user do
    username "DVG"
    email "dvg@example.com"
    password "secret"
    password_confirmation { password }
  end
end
```
