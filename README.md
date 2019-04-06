# Tutorial

Run:

```sh
rails new rails_tailwind --skip-coffee --webpack -d postgresql
cd rails_tailwind
```

You need to run your rails server and webpack in two terminal windows unless you a gem like forman or Docker.

For now, we will just create two terminal windows. In one window, run:

```sh
rails s
```

and in the other:

```sh
./bin/webpack-dev-server
```

You should see rails welcome page if you navigate to `http://localhost:3000` in your browser.

Now run:

```sh
rails generate controller Home index
```

You can remove the generated JS and Sass file, we won't be needing them. Set your `routes.rb` file to:

```rb
# frozen_string_literal: true

Rails.application.routes.draw do
  root 'home#index'
  resources :home, only: :index
end
```

Now, it is time to add TailwindCSS.
