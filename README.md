# codr.academy-step-by-step-guide
A step-by-step guide on how codr.academy was built


## Create New
`rails new codr.academy -d postgresql`

## Config Database

Update config/database.yml

```
default: &default
  host: localhost
  port: 5432
  username: <%= Rails.application.credentials.dig(:db, :username) %>
  password: <%= Rails.application.credentials.dig(:db, :password) %>
```

`EDITOR="nano" rails credentials:edit`

```
db:
  username: [username]
  password: [password]
```

- `rails db:setup`
- `rails db:migrate`

## Home Page with Title Bar

`rails g controller HomePage index`

config/routes.rb

```
root 'home_page#index'
```

cp irvan-smith-5eBW5GomfhY-unsplash to app/assets/images

app/views/layouts/application.html.erb

```
    <h1>codr.academy</h1>
```

app/assets/stylesheets/application.css

```
@import url('https://fonts.googleapis.com/css2?family=Anonymous+Pro:wght@700&display=swap');

body {
    margin: 0;
}

h1 {
    background-image: url("irvan-smith-5eBW5GomfhY-unsplash.jpg"); /* Photo by Irvan Smith on Unsplash */
    background-position: center;
    background-repeat: no-repeat;
    background-size: cover;
    color: white;
    font-family: 'Anonymous Pro', monospace;
    text-shadow: 0px 0px 1rem orange;
    font-size: 3.5rem;
    margin: 1rem 0rem;
    padding: 1rem 3rem;
}
```

app/views/home_page/index.html.erb

```
<h2>HomePage#index</h2>
```

## Create Models

- `rails g scaffold user username name cohort point:integer github linked_in`

- `rails g scaffold post title content user:references edited:boolean time:datetime`

- `rails g scaffold comment user:references post:resources content edited:boolean`

- `rails g scaffold VerbalMark user:references post:references comment:references verbal_mark`

- add relations to models according to ERD/Plan ie `belongs_to, has_many, dependant: :destroy`, etc 

## Add Images using S3bucket

Add Pictures to posts
- `models/post.rb` - has_one_attached :picture (or whatever you want to name it)
- `views/posts/_form` 
    -  ``` <div>
            <%= form.label :picture %>
            <%= form.file_field :picture, accept: "image/png,image/gif,image/jpeg, image/jpg, image/webp" %>
        </div> (accept is optional)
    - `/show`
        - `<%= image_tag @post.picture, width: "100" if @post.picture.attached?   %>`

- `posts_controller.rb` in create and update methods
    - `@post.picture.attach(params[:post][:picture])`


- `rails active_storage:install`
    -change migration version to [6.0]
- `rails db:migrate`
- Create s3 bucket on aws website
    - Made not public (this time)
    - Created IAM user
        - Programmatic access
        - Attached existion policy, S3FullAccess
- `bundle add aws-sdk-s3`
- `EDITOR="nano" rails credentials:edit`
- ` aws:
    access_key_id: [ask luke]
    secret_access_key: [ask luke] `
- edit `storage.yml` to
    - amazon:
  service: S3
  access_key_id: <%= Rails.application.credentials.dig(:aws, :access_key_id) %>
  secret_access_key: <%= Rails.application.credentials.dig(:aws, :secret_access_key) %>
  region: ap-southeast-2
  bucket: hackathon-rails
- `config/enviornments/development.rb and or production.rb`
- edit `config.active_storage.service` from `:local` to `:amazon` 


## Devise Setup

- `rails g devise:install`
- follow the step in the terminal
    - added `config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }` config/environments/development.rb
    - Setup a root e.g `root 'home_page#index'`
    - Added the example flash notes shown in terminal to app/views/layou/application.html.erb  

- `rails g devise:views` to create the users login and signup views and more
- `rails g devise users` correlates devise to users

## Deploy to Heroku

- `brew tap heroku/brew && brew install heroku `
- `heroku autocomplete`
- `printf "$(heroku autocomplete:script zsh)" >> ~/.zshrc; source ~/.zshrc`
- `compaudit -D`
- `heroku create codr.academy` 
- `git push heroku master`

### Fix missing images

config/environments/production.rb

```
config.assets.initialize_on_precompile = false
  config.assets.compile = true
```

