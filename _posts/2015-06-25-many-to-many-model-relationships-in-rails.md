---
layout: post
title:  "Many to many model relationships in Rails"
date:   June 15, 2015
permalink: /many-to-many-model-relationships-in-rails/
tags:
  - Ruby
  - Rails
  - ActiveRecord
categories:
  - Ruby on Rails
comments: true
---

One of the challenges I had early in learning Rails was setting up many-to-many relationships between models. Many tutorials and articles cover the simpler one-to-many and many-to-one relationships with ActiveRecord's `has_many` and `belongs_to` functions, respectively, but when trying to build anything more than a sample application you quickly realize that you need to set up slightly more complicated relationships.

### Problem

I want to set up a many to many relationship between my **user** and **team** models where <u>users can belong to many teams</u> and <u>teams can contain many users</u>. Prior to setting up the relationship, my models are just basic ActiveRecord models generated from the rails model generator.

**user.rb model (before)**

```ruby
class User < ActiveRecord::Base
end
```

**team.rb model (before)**

```ruby
class Team < ActiveRecord::Base
end
```

### Solution

To enforce the many to many relationship, I chose to implement the one of the ways suggested by the [RailsCast Many-to-Many screencast](http://railscasts.com/episodes/47-two-many-to-many){:target="_blank"}, which involves **`has_many :through`**.

This approach involves creating another model specifically for handling the relationship mappings so that ActiveRecord can join the tables behind the scenes. Because I'm trying to join the users and teams table, I decided to name this model **membership**.

Running `rails g model membership user_id:integer team_id:integer` will create a model and migration for the new membership model.

Now, add lines 2 and 3 to set up the relationships to the user and team models.

**membership.rb model (after)**

```ruby
class Membership < ActiveRecord::Base
   belongs_to :user
   belongs_to :team
end
```

The membership model with the relationships above will represent another table that will be used for storing mappings between the user and team models. For example, the table may include data such as:

**Sample membership table data**

| user_id | team_id |
|--------|--------|
| 1 | 1 |
| 2 | 1 |
| 3 | 1 |
| 4 | 1 |
| 1 | 2 |
| 1 | 3 |
| 2 | 2 |

This sample data shows how the model can be used to look up many to many relationships.

- User 1 has many teams 1, 2, and 3
- Team 1 has many users 1, 2, 3, and 4

Meow, we can associate both the user and team models to have many memberships. In addition, we can use a `has_many` override to tell active record that "a user has_many teams through the memberships model" and "a team has_many users through the memberships model".

**user.rb (after)**

```ruby
class User < ActiveRecord::Base
  has_many :memberships, :dependent => :destroy
  has_many :teams, through: :memberships
end
```

**team.rb (after)**

```ruby
class Team < ActiveRecord::Base
  has_many :memberships, :dependent => :destroy
  has_many :users, through: :memberships
end
```

Now that the relationships are set up, assuming that you have ran `rake db:migrate` to sync the new membership model, each model should be able to take advantage of the relationship.

```ruby
@team = Team.first
@team.users # array of users

@user = User.first
@user.teams # array of teams
```
