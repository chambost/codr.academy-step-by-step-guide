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

# Home Page

`rails g controller HomePage index`

config/routes.rb

```
root 'home_page#index'
```

