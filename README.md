# Tutorial

For the purpose of this tutorial, we will assume you have Ruby and the Rails gem installed. Please visit the [Getting Started with Rails Guide](https://guides.rubyonrails.org/getting_started.html) if you do not.

## Create a new Rails project

```sh
rails new rails_tailwind --skip-coffee --webpack -d postgresql
cd rails_tailwind
rails db:create
```

This will create a new Rails project for you with webpack and postgres configured for you and create our databases. We will not use coffeescript, which is why we add the `--skip-coffee` flag. You can also ommit the `-d postgresql` flag if you like, but if you want to deploy to something like Heroku, I would recommend adding it.

## Running Rails and Webpack

You need to run the Rails server and webpack-dev-server in two terminal tabs/windows unless you use Docker or a gem like Foreman.

For now, we will just create two terminal windows. In one window, run:

```sh
rails s
```

and in the other:

```sh
./bin/webpack-dev-server
```

You should see rails welcome page if you navigate to `localhost:3000` in your browser.

![rails default information page](https://guides.rubyonrails.org/images/getting_started/rails_welcome.png)

## Generate a Home controller

In order to see the TailwindCSS styles that we will integrate later, we at minimum need a controller and view.

```sh
rails generate controller Home index
```

You can remove the generated JS, SCSS, and helper file, we won't be needing them.

```sh
rm app/helpers/home_helper.rb app/assets/javascripts/home.js app/assets/stylesheets/home.scss
```

## Configure your routes

Change your `config/routes.rb` file to:

```rb
# frozen_string_literal: true

Rails.application.routes.draw do
  root 'home#index'
  resources :home, only: :index
end
```

Restart your Rails server, and now you should see the following on `localhost:3000`

![home index](https://i.imgur.com/A47j9dx.png)

## Install TailwindCSS

_Note: At the time of writing this, TailwindCSS v1.0 is still in beta so we will be installing tailwind@next. If v1.0 is out when are doing this and I have not updated this tutorial, you can omit `@next`._

Run the following commands in your terminal:

```sh
yarn add tailwindcss@next --dev
./node_modules/.bin/tailwind init
```

This should add Tailwind to your `package.json` as well as create a `tailwind.config.js` file at the root of your project. This file can be used to customize the tailwind defaults. Read more [here](https://next.tailwindcss.com/docs/configuration)

Next, add the following two lines to `postcss.config.js`

```js
require('tailwindcss'),
require('autoprefixer'),
```

Your `postcss.config.js` file should now look like this:

```js
module.exports = {
  plugins: [
    require('tailwindcss'),
    require('autoprefixer'),
    require('postcss-import'),
    require('postcss-flexbugs-fixes'),
    require('postcss-preset-env')({
      autoprefixer: {
        flexbox: 'no-2009'
      },
      stage: 3
    })
  ]
}
```

## Configure Tailwind

_There are a few ways you can do this but this is my personal preference._

Remove the assets folder:

```sh
rm -rf app/assets
```

Rename the `app/javascript` directory:

```sh
mv app/javascript app/frontend
```

Tell webpacker to use this new folder by changing the source_path in `config/webpacker.yml` from: `source_path: app/javascript` to `source_path: app/frontend`.

Next, we need to setup our stylesheets:

```sh
touch app/frontend/packs/stylesheets.css
```

Paste the following into our new `stylesheets.css` file. _This is straight from the [tailwind docs](https://next.tailwindcss.com/docs/installation#step-2-add-tailwind-to-your-css)_

```css
@tailwind base;

@tailwind components;

@tailwind utilities;
```

Add the following line in `app/frontend/packs/application.js`:

```js
import './stylesheets.css'
```

The last step is to tell Rails to use our packs. In `app/views/layouts/application.html.erb`, change:

```erb
<%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
<%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
```

to:

```erb
<%= stylesheet_pack_tag 'stylesheets', 'data-turbolinks-track': 'reload' %>
<%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
```

Restart the Rails server and webpack-dev-server and you should now see the following on `localhost:3000`

![tailwind home index](https://i.imgur.com/C64oFFy.png)

Tailwind should now be working so lets tweak our views to see some Tailwind goodness.

## Update views to use TailwindCSS

In `app/views/layouts/application.html.erb` change:

```erb
<body>
  <%= yield %>
</body>
```

to:

```erb
<body class="min-h-screen bg-gray-100">
  <div class="container mx-auto">
    <%= yield %>
  </div>
</body>
```

and in `app/views/home/index.html.erb` change:

```erb
<h1>Home#index</h1>
<p>Find me in app/views/home/index.html.erb</p>
```

to:

```erb
<section class="py-8 text-center">
  <h1 class="text-5xl mb-2">Ruby on Rails + TailwindCSS</h1>
  <p class="text-xl mb-8">❤️ A match made in heaven️️ ❤️</p>
  <a href="https://next.tailwindcss.com" class="bg-teal-500 hover:bg-teal-700 text-white font-bold py-4 px-8 rounded">Tailwind Docs</a>
</section>
```

You should now see the following page when you navigate to `localhost:3000`

![updated tailwind home index](https://i.imgur.com/okfqCoS.png)

And now you have TailwindCSS working in your Rails app!

Happy coding!
