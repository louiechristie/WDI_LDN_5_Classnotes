# Scopes
  - using the "joins_etc" app from Tuesday...

#### post.rb  
scope :published, where('published_at is not null')

- and you can join them:  
`scope :published, where('published_at is not null').joins(:category)`


but when you join, you have to make sure SQL field names are not ambiguous:  
  `Post.published.joins(:comments)`

  `scope :published, where('posts.published_at is not null')`


- you can chain scopes  
  `scope :published_and_commented, published.joins(:comments).group('comments.post_id').having('count(*) > 0')`


- and you can call them on the class:  

  `Post.published_and_commented`

- or on an association of the class objects:  

  `Category.first.posts.published`



- Working with times  
  - If you’re working with dates or times within scopes, due to how they are evaluated, you will need to use a lambda so that the scope is evaluated every time.

    `scope :last_week, lambda { where("published_at > ?", Time.zone.now.ago(1.week) ) }`

  - Without the lambda, this Time.zone.now will only be called once in production environment - and you end up with the wrong records...


- When a lambda is used for a scope, it can take arguments:

  `scope :one_week_before, lambda { |time| where(published_at: (time.ago(1.week)..time) }`

  `Post.one_week_before(Time.zone.now)`

- However, this is just duplicating the functionality that would be provided to you by a class method.

```
  class Post < ActiveRecord::Base
    def self.one_week_before(time)
      where(published_at: (time.ago(1.week)..time)
    end
  end
```

- Using a class method is the preferred way to accept arguments for scopes. These methods will still be accessible on the association objects:

`  Category.first.posts.one_week_before(Time.zone.now)`

- you can set a "default_scope"

  `default_scope where("posts.published_at IS NOT NULL").order(:published_at).reverse_order`

- You can remove all scopes with ".unscoped" - particularly for when removing a default_scope
