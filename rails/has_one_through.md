`has_one :through` instead of `belongs_to :through`
====

I have a data model like the following:

```ruby
class User
  has_many :blogs
end

class Blog
  belongs_to :user
  has_many :posts
end

class Post
  belongs_to :blog
  has_many :comments
end

class Comment
  belongs_to :post
end
```

I want an instance method called `Comment#user` that will return the `User` whose blog the comment was posted to.  The standard way to do this is with `delegate`:

```ruby
class User
  has_many :blogs
end

class Blog
  belongs_to :user
  has_many :posts
end

class Post
  belongs_to :blog
  has_many :comments

  delegate :user, to: :blog
end

class Comment
  belongs_to :post

  delegate :user, to: :post
end
```

The problem of course is that I'm loading up a bunch post and blog records and then doing nothing with them.  It's super inefficient.  There is no reason I can't do this in one SQL query:

```ruby
class Comment
  belongs_to :post

  def user
    User.joins(blogs: :posts).find_by('posts.id = ?', post_id)
  end
end
```

The problem is that my data model is a lot bigger than this and I would need to define a method like this about 50 times.  I wanted to use `belongs_to :through` but it turns out that for no obvious reason `belongs_to :through` isn't a thing.  `has_one :through` is though and it does what I want:

```ruby
class User
  has_many :blogs
  has_many :posts, through: :blogs
  has_many :comments, through: :posts
end

class Blog
  belongs_to :user
  has_many :posts
  has_many :comments, through: :posts
end

class Post
  belongs_to :blog
  has_many :comments
  has_one :user, through: :blog
end

class Comment
  belongs_to :post
  has_one :blog, through: :post
  has_one :user, through: :blog
end
```

And now I can get from any record in my data model to record(s) of any other type associated with it in a single query!
