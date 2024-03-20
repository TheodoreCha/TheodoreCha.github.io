---
layout: post
title: "Race Condition"
subtitle: "Ruby on Rails"
date: 2024-03-20
author: "Theo Cha"
tags:
  - Backend
---

> “Race condition
> A race condition or race hazard is the condition of an electronics, software, or other system where the system's substantive behavior is dependent on the sequence or timing of other uncontrollable events, leading to unexpected or inconsistent results. - Wikipedia -”

### What is race condition?

As the below image, most Rails application has multiple processes such as API requests, Rake tasks, and background jobs. all these processes can access to the shared resource. Race conditions occur when multiple processors access the shared resources.

![Multiple processors](/img/post/processors.png "Multiple processors")

###### Read-modify-write race condition:

A read-modify-write race condition is one of the most common race conditions in many web applications. here is an easy example to explain in a real-life situation in the Rails application. Let's say there are two users and one shared resource - `Idea`.

1. User A accessed `@idea = Idea.find(params[:id])`
2. User A incremented total 1 vote `@idea.votes += 1`
3. User B accessed `@idea = Idea.find(params[:id])`
4. Just before User A saves the vote, User B incremented total 1 vote `@idea.votes += 1`
5. User A saved total 1 vote
6. User B saved total 1 vote

```ruby
class IdeasController < ActionController::Base
  def vote
    # this part calls a critical area
    @idea = Idea.find(params[:id])
    @idea.votes += 1
    @idea.save!

    redirect_to ideas_path
  end
end
```

As you can see, the total vote should be 2 instead of 1. the problem is that before A user's vote was saved, user B had access to the shared resource with the wrong vote data.

---

### How to fix

##### 1. Remove the critical area

By refactoring, we can use an atomic operation. in other word, there is no process like reading and modifying, while the operation is running.

- From:

```ruby
@idea = Idea.find(id)
@idea.votes + 1
@idea.save!
```

```sql
UPDATE "ideas"
SET "votes" = 1
WHERE "ideas"."id" = 1
```

- To:

```ruby
Idea.where(id: id).update_all('"votes" = "votes" +)
```

```sql
UPDATE "ideas"
SET "votes" = "votes" + 1
WHERE "ideas"."id" = 1
```

##### 2. Detect and recover

Rails Active record support a Optimistic locking. using this, Rails will only apply an update to the database, if it is the same version of object in your memory. this can be a bit risky because it can silently ignore update.

To enable the optimistic locking:

1. Add a `lock_version` column in database.

```ruby
change_table :ideas do |t|
  t. integer :lock_version, default: 0
end
```

with this column, Rails will immediately pick it up and start using the optimistic locking.

```ruby
class IdeasController < ActionController::Base
  def vote
    # this part calls a critical area
    @idea = Idea.find(params[:id])
    @idea.votes += 1
    @idea.save!

    redirect_to ideas_path
    rescue ActiveRecord::StableObjectError
      retry
  end
end
```

```sql
UPDATE "ideas"
SET   "votes" = 1,
      "lock_version" = 1

WHERE "ideas"."id" = 1
  AND "ideas". "lock_version" = 0
```

##### 3. Protect critical area

Rails support a pessimistic lock to protect the critical area. with pessimistic locking, a process is telling the database that once you have extracted an object from the database, only it has access to it till a transaction is completed. if two request are coming, one of request will wait outside.

```ruby
class IdeasController < ActionController::Base
  def vote
    Idea. transaction do
    @idea = Idea.lock.find(params [:id])
    @idea.votes += 1 @idea.save!
  end

  redirect_to ideas_path
  end
end
```

```sql
SELECT *
FROM "ideas"
WHERE "ideas"."id" = 1
LIMIT 1
FOR UPDATE
```

- With `with_advisory_lock`

```ruby
class IdeasController < ActionController::Base
  def vote
    Idea.with_advisory_lock("idea_vote_#{params|:id]}") do
    @idea = Idea.find(params|:id])
    @idea.votes += 1 @idea.save!
  end

  redirect_to ideas_path
  end
end
```

---

## References

- <https://www.youtube.com/watch?v=jEDX3yswrcM&t=2197s>
