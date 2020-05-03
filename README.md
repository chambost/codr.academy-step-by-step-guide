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






## Website pages layout

### _NavBar (on all pages)(partial)
- Home(Link)
- Profile(Link)
- Sign out
- Chat room page(link)
    - all cohorts, brisbane , sydney and melb links)
- Cohort(Link) of all the cohorts)
- Search bar? 
- Others?
- Signin(link)
- Login(link)


### Home page (controller) (using posts model?)
- list of posts with signup/login links
- clickable posts for read on new page or same page? and comment on

    - Seperate page for each clicked post??? (home_page/id??)

### Profile page (model and controller?)
- Personal information CRUD 
- Form 
- Avatar
- Others?

### Chatroom Page (model and controller?)
- Chatbook for all cohorts(like on jasons website)
- links to Brisbane/Sydney/Melbourne private chat?
- links to chats with one student?

### Cohort Page (controller?) or just use the (user/index or something like that)
- Just a list of all the coherts names and maybe email or other info nation wide?

### Signin Page (devise?)
- Form

### Login Page (devise)
- Form









