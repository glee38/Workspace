#Meaningful Code

If you've read my first post, then you know that about a month ago, I gave birth to my first ever code child - Teavana CLI Gem. I was in labor for days, but quickly forgot about my exhaustion upon the sweet arrival of my first code baby. Today, I am excited to share the birth of my second code child, Meaningful Code. The labor period was a little longer this time around, but the birth was that much more rewarding. 

After learning about a smooth little fella who goes by the name "Sinatra", I was challenged to create a Sinatra app from scratch. I knew this was the perfect opportunity to create a platform for something I was passionate about, and with my newly aquired skills, I was more than excited to see my idea to fruition. Below, I will take you along my journey, once again sharing my struggles and triumphs. I hope you enjoy.

##Getting started

For this particular project, I was given the following requirements:

1. Build an MVC Sinatra Application.
2. Use ActiveRecord with Sinatra.
3. Use Multiple Models.
4. Use at least one `has_many` relationship
5. Must have user accounts. The user that created the content should be the only person who can modify that content
6. Models must have validations to ensure that bad data isn't created
7. Any validation failures must be shown to user with an error message

By now, I had grown accustomed to building app structures and environments from scratch, and I no longer felt intimidated by the technicalities of the project. My biggest concern was what my app would be about. I knew I wanted to make something that would not only help me understand code better, but also help others in some way. 

##What is Meaningful Code?

After a much thought, I finally came up with an idea for my Sinatra app. I wanted to create a website to aid budding nonprofit organizations with their web and/or mobile development needs. My goal was to build a platform in which developers and nonprofits could unite for a common cause.

Nonprofit organizations that did not have the resources to hire developers would upload their web/mobile development needs in the form of "projects" to the website, while developers could sign up and volunteer to work on a project (or projects) of their choosing. My hope was that by creating a platform to bring people together, we would, together, make a meaningful and lasting difference in the lives of others.

##Beginning the Labor
Once I had my idea, it was time to set it into motion. I began by whiteboarding the basics of my application with the following associations and attributes:

####1. Developer
Assocations & Validations:

```ruby
class Developer < ActiveRecord::Base
  has_secure_password
  validates_presence_of :name, :github, :username, :email, :password_digest, :about_me
  validates_uniqueness_of :username, :email, :github
  validates_format_of :email, :with => /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/i, :on => :create

  has_many :developer_projects
  has_many :projects, through: :developer_projects
  has_many :nonprofits, through: :projects
  has_many :messages

  include Slugify
  extend Slugify
end
```

Attributes:

```ruby
  create_table :developers do |t|
    t.string :name
    t.string :github
    t.string :username
    t.string :password_digest
    t.string :email
    t.text   :about_me
  end
```

####2. Nonprofit
Assocations & Validations:

```ruby
class Nonprofit < ActiveRecord::Base
  has_secure_password
  validates_presence_of :name, :cause, :tagline, :website, :username, :email, :password_digest
  validates_uniqueness_of :username, :email, :website
  validates_format_of :email, :with => /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/i, :on => :create

  has_many :projects
  has_many :developers, through: :projects
  has_many :messages

  include Slugify
  extend Slugify

  def self.digest(string)
    cost = ActiveModel::SecurePassword.min_cost ? BCrypt::Engine::MIN_COST :
                                                  BCrypt::Engine.cost
    BCrypt::Password.create(string, cost: cost)
  end
end
```

Attributes:

```ruby
  create_table :nonprofits do |t|
    t.string :name
    t.text   :cause
    t.text   :tagline
    t.string :website
    t.string :username
    t.string :password_digest
    t.string :email
  end
```

####3. Projects
Assocations & Validations:

```ruby
class Project < ActiveRecord::Base
  validates_presence_of :name, :project_specs, :due_date
  
  belongs_to :nonprofit
  has_many :developer_projects
  has_many :developers, through: :developer_projects

  def slug
    self.name.downcase.strip.gsub(" ", "-")
  end

  def self.find_by_slug(slug)
    self.all.find {|obj| obj.slug == slug}
  end
end
```


####4. Developer_Projects
Assocations:

```ruby
class DeveloperProject < ActiveRecord::Base
  self.table_name = "developer_projects"
  belongs_to :developer
  belongs_to :project
end
```

Attributes:

```ruby
  create_table :developer_projects do |t|
    t.integer :developer_id
    t.integer :project_id
  end
```

####5. Messages
**NOTE**: The messaging feature was added last, and though it was not required, I felt that it only made sense for developers and nonprofit organizations to have a method of communication with one another within the scope of the website. I would have loved to build a more complex and efficient version of this feature, but due to time constraints and my limited knowledge of code at the time, I felt that a basic implementation of this feature would suffice for the time being.

Associations & Validations:

```ruby
class Message < ActiveRecord::Base
  validates_presence_of :recipient, :subject, :content

  belongs_to :developer
  belongs_to :nonprofit
end
```

Attributes:

```ruby
  create_table :messages do |t|
    t.string  :sender
    t.string  :recipient
    t.string  :subject
    t.text    :content
    t.integer :developer_id
    t.integer :nonprofit_id
    t.date    :date
  end
```

###MVCs
Once I had sucessfully set up my associations and databases, I dove into 
