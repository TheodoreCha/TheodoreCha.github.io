---
layout: post
title: "How to test ActiveJob is enqueued in a controller?"
subtitle: "Rails ActiveJob"
date: 2022-08-05
author: "Theo Cha"
# header-img: "img/post-bg-2015.jpg"
tags:
    - Ruby on Rails
    - Rspec
---

> “Yeah It's pretty straightforward.”

- **How to test ActiveJob is enqueued in a controller?**

I created an ActiveJob and now we want to call the job in our controller. I want to test if the job is called properly. In my case, I call the job multiple times so I need to test the number of calls.

Code example
----------------------------------------

Let's say we have a create action that calls an ActiveJob if the record is successfully saved.

```cpp
class ExampleClass < ApplicationController
  def create
    @object = Object.new(importer_params)
    respond_to do |format|
      if @object.save
        MyJob.perform_later( @object.id )
        format.html { redirect_to @object, notice: t('.notice') }
      else
        format.html { render :new }
      end
    end
  end
end
```

Here is how to test the job in the controller.

```cpp
  expect {
    post :create, params: { id: id }
  }.to have_enqueued_job(MyJob).exactly(1)
```

Now, Look at line 45 - with the `exactly(1)`, you can test the number of calls


### [More Rspec Matchers](https://www.rubydoc.info/gems/rspec-rails/RSpec%2FRails%2FMatchers%3Ahave_enqueued_job)

Examples:

```cpp
expect {
  HeavyLiftingJob.perform_later
}.to have_enqueued_job

# Using alias
expect {
  HeavyLiftingJob.perform_later
}.to enqueue_job

expect {
  HelloJob.perform_later
  HeavyLiftingJob.perform_later
}.to have_enqueued_job(HelloJob).exactly(:once)

expect {
  3.times { HelloJob.perform_later }
}.to have_enqueued_job(HelloJob).at_least(2).times

expect {
  HelloJob.perform_later
}.to have_enqueued_job(HelloJob).at_most(:twice)

expect {
  HelloJob.perform_later
  HeavyLiftingJob.perform_later
}.to have_enqueued_job(HelloJob).and have_enqueued_job(HeavyLiftingJob)

expect {
  HelloJob.set(wait_until: Date.tomorrow.noon, queue: "low").perform_later(42)
}.to have_enqueued_job.with(42).on_queue("low").at(Date.tomorrow.noon)

expect {
  HelloJob.set(queue: "low").perform_later(42)
}.to have_enqueued_job.with(42).on_queue("low").at(:no_wait)

expect {
  HelloJob.perform_later('rspec_rails', 'rails', 42)
}.to have_enqueued_job.with { |from, to, times|
  # Perform more complex argument matching using dynamic arguments
  expect(from).to include "_#{to}"
}
```

### [Have_enqueued_mail matcher](https://relishapp.com/rspec-staging/rspec-rails/docs/matchers/have-enqueued-mail-matcher)

Plus: Working with mailer matcher

**Checking mailer class and method name**

```cpp
require "rails_helper"

RSpec.describe NotificationsMailer do
  it "matches with enqueued mailer" do
    ActiveJob::Base.queue_adapter = :test
    expect {
      NotificationsMailer.signup.deliver_later
    }.to have_enqueued_mail(NotificationsMailer, :signup)
  end
end
```

References
----------

- <https://www.rubydoc.info/gems/rspec-rails/RSpec%2FRails%2FMatchers%3Ahave_enqueued_job>
- <https://relishapp.com/rspec-staging/rspec-rails/docs/matchers/have-enqueued-mail-matcher>













