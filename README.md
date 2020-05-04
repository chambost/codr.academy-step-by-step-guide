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

- add relations to models according to ERD/Plan ie `belongs_to, has_many, dependant: :destroy`, etc 

## Add Images(only started)

- 'rails active_storage:install'
    -change migration version to [6.0]

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

## GitHub Omniauth authentication

Add fields to users model

- `bundle add omniauth-github`
- `rails g migration AddOmniauthToUsers provider:string uid:string`
- `rails db:migrate`

Create GitHub tokens

- GitHub | Settings | Developer Settings | OAuth Apps | Register a new application
- Application name: codr.academy
- Homepage URL: http://codr.academy
- Description: [description]
- Authorisation Callback URL: http://codr.academy/users/auth/github/callback

config/initializers/devise.rb

```
  config.omniauth :github, ENV['GITHUB_APP_ID'], ENV['GITHUB_APP_SECRET'], scope: 'user'
```

Set Config Vars in Heroku

```
GITHUB_APP_ID [id]
GITHUB_APP_SECRET [secret]
```

Make user model Omniauthable
app/models/user.rb

```
devise :omniauthable, omniauth_providers: %i[github]
```

Sign-up link
app/views/devise/sessions/new.html.erb

```
  <div>
    <%= link_to "Sign in with Github", user_github_omniauth_authorize_path %>
  </div>
```

config/routes.rb

```
devise_for :users, controllers: { omniauth_callbacks: 'users/omniauth_callbacks' }
```

app/controllers/users/omniauth_callback_controller.rb

```
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
    def github
      # You need to implement the method below in your model (e.g. app/models/user.rb)
      @user = User.from_omniauth(request.env["omniauth.auth"])
  
      if @user.persisted?
        sign_in_and_redirect @user, event: :authentication #this will throw if @user is not activated
        set_flash_message(:notice, :success, kind: "Github") if is_navigational_format?
      else
        # @user.email = request.env["omniauth.auth"]["extra"]["all_emails"][0]["email"]
        # user.password = Devise.friendly_token[0, 20]
        # @user.name = request.env["omniauth.auth"]["info"]["name"]
        # @user.username = request.env["omniauth.auth"]["info"]["nickname"]
        # @user.github = "https://github.com/#{@user.username}"
        # @user.save
        # sign_in_and_redirect @user, event: :authentication #this will throw if @user is not activated
        # set_flash_message(:notice, :success, kind: "GitHub") if is_navigational_format?
        redirect_to new_user_registration_url
      end
    end
  
    def failure
      redirect_to root_path
    end
end
```

app/models/user.rb


```
  def self.from_omniauth(auth)
    where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
      user.email = auth["extra"]["all_emails"][0]["email"]
      user.password = Devise.friendly_token[0, 20]
      user.name = auth["info"]["name"]
      user.username = auth["info"]["nickname"]
      user.github = "https://github.com/#{user.username}"
      # user.name = auth.info.name   # assuming the user model has a name
      # user.image = auth.info.image # assuming the user model has an image
      # If you are using confirmable and the provider(s) you use validate emails, 
      # uncomment the line below to skip the confirmation emails.
      # user.skip_confirmation!
    end
  end
```

To fix a heroku error switch from zietwerk to classic autoloader
config/application.rb

```
    config.autoloader = :classic
```

Deploy

```
git push heroku master
heroku run rails db:migrate
```

If app crashes, see error messages via

```
heroku run rails console
```