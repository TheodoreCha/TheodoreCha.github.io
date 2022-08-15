---
layout: post
title: "TDD GraphQL on Rails"
subtitle: "Ruby on Rails with Rspec"
date: 2022-08-15
author: "Theo Cha"
tags:
    - Ruby on Rails
    - Rspec
    - GraphQL
---


> Let's do TDD with GraphQL + Rails + Rspec

![GraphQL is cool](/img/post/graphql-is-cool.jpeg "GraphQL is cool")

### Introduction
----------------------------------------

It has been a long time working with Rails + GraphQL technologies. so, I want to recap the Rails + GraphQL and summarize a few points by writing this public tutorial.

In this article, I will use the following technologies:

- Ruby on Rails
- GraphQL gem
- Rspec

### Getting started
----------------------------------------

**Installation**:
- Install Rails and set up GraphQL gem by following [this tutorial](https://www.howtographql.com/graphql-ruby/1-getting-started/). we assume you create a Link model, controller, view, and records.

**Focus points**
- TDD approach to create GraphQL.
- Create queries feature.
- Create mutations feature.

### Queries
----------------------------------------

#### Setup specs

```cpp
require 'rails_helper'
RSpec.describe Link, type: :request do
  describe 'GraphQL query for looking up links' do
    it 'returns links' do
      Link.create(url: 'http://dev.apollodata.com/', description: 'Awesome GraphQL Client')
      query = <<-GRAPHQL
        query {
          allLinks {
            id
            url
          }
        }
      GRAPHQL
      post '/graphql', params: { query: query }

      json = JSON.parse(response.body)
      data = json['data']['allLinks']

      expect(data).to eq([{"id"=>"1", "url"=>"http://dev.apollodata.com/"}])
    end
  end
end
```

Run the spec file and yes, fail first.

#### Setup Query type
```cpp
module Types
  class QueryType < BaseObject
    # queries are just represented as fields
    # `all_links` is automatically camelcased to `allLinks`
    field :all_links, [LinkType], null: false

    # this method is invoked, when `all_link` fields is being resolved
    def all_links
      Link.all
    end
  end
end

```

### Mutations
----------------------------------------
#### Setup a service

Before setting up the spec file, I want to decouple a creation logic from the GraphQL resolver because:

- Thin Rails controller and GraphQL resolver: they are mainly responsible for understanding requests and responses.
- Re-usable services
- Testable: Since the logic in a controller or resolver is thinner and simpler, it becomes really easy to test. also, testing business processes becomes more isolated. it is easier to stub and check whether specific processes are invoked within our service.

If you are interested in how the service logic works, please visit [Why do you need service objects](https://theodorecha.github.io/2022/08/11/why-do-you-need-service-objects/)

Let's create a service spec file and fail first.

```cpp
# `spec/services/link_create_spec.rb`
require 'rails_helper'
describe LinkCreate do
  it "creates a user and sends an email verification email" do
    result = nil

    expect {
      result = described_class.call(
        url: 'http://dev.apollodata.com/',
        description: 'Awesome GraphQL Client'
      )
    }.to change(Link, :count).by(1)

    list = Link.last!
    expect(result[:url]).to eq(list.url)
  end
end
```

#### Create a service object
```cpp
class LinkCreate
  def self.call(...)
    new(...).call
  end

  def initialize(url:, description:)
    @url = url
    @description = description
  end

  def call
    link = Link.new(
      url: @url,
      description: @description
    )

    if link.save
      link
    else
      'fail'
    end
  end
end
```

Now, you run the spec file again and all test pass.

#### Setup mutations spec

We are going to create a spec file for the mutations. since we decoupled the creation logic from the Mutation's resolver, we just need to make sure whether the `LinkCreate` service is executed.

```cpp
#spec/graphql/mutations/create_link.rb
require "rails_helper"

RSpec.describe Mutations::CreateLink do
  describe  do
    it "calls LinkCreate service" do
      allow(LinkCreate).to receive(:call)
      query = <<-GRAPHQL
        mutation CreateLink($url: String!, $description: String!) {
          createLink(
            url: $url
            description: $description
          ) {
            id
            url
          }
        }
      GRAPHQL
      GraphqlTutorialSchema.execute(query, variables: {
        url: "test@example.com",
        description: "Example"
      })

      expect(LinkCreate).to have_received(:call).with(
        url: 'test@example.com',
        description: 'Example'
      )
    end
  end
end
```

Run the spec file and yes, fail first.

#### Mutation

Add the `create_link` filed at `mutation_type.rb`

```cpp
#app/graphql/types/mutation_type.rb
module Types
  class MutationType < BaseObject
    field :create_link, mutation: Mutations::CreateLink
  end
end
```
And here is the create_link mutation file.

```cpp
#app/graphql/mutations/create_link.rb
module Mutations
  class CreateLink < BaseMutation

    argument :description, String, required: true
    argument :url, String, required: true

    type Types::LinkType

    def resolve(description: nil, url: nil)
      LinkCreate.call(
        url: url,
        description: description
      )
    end
  end
end
```

Now, run `rspec spec/graphql/mutations/create_link.rb`, then all test will pass.

References
----------

- <https://www.howtographql.com/graphql-ruby/3-mutations/>
- <https://github.com/phawk/coinfusion>
- <https://github.com/TheodoreCha/graphql-practice>









