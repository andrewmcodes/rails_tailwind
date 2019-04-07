# Tutorial

Run:

```sh
rails new rails_tailwind --skip-coffee --webpack -d postgresql
cd rails_tailwind
```

You need to run your rails server and webpack in two terminal windows unless you a gem like Forman or Docker.

For now, we will just create two terminal windows. In one window, run:

```sh
rails s
```

and in the other:

```sh
./bin/webpack-dev-server
```

You should see rails welcome page if you navigate to `http://localhost:3000` in your browser.

*Insert image*

Now run:

```sh
rails generate controller Home index
```

You can remove the generated JS, SCSS, and helper file, we won't be needing them.

```sh
rm app/helpers/home_helper.rb app/assets/javascripts/home.js app/assets/stylesheets/home.scss
```

Set your `routes.rb` file to:

```rb
# frozen_string_literal: true

Rails.application.routes.draw do
  root 'home#index'
  resources :home, only: :index
end
```

Now, it is time to add TailwindCSS.

Run the following command in your terminal:

```sh
yarn add tailwindcss --dev
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

Now we need to tell our app to use tailwind. There are a few ways to do this but this is how I like to do it

1. Remove the assets folder

```sh
rm -rf app/assets
```

2. Rename the `app/javascript` directory

```sh
mv app/javascript app/frontend
```

and then tell webpacker to use this new folder by changing the source_path in `config/webpacker.yml`

from

```yml
default: &default
  source_path: app/javascript
```

to

```yml
default: &default
  source_path: app/frontend
```

Next, we need to setup our stylesheets

```sh
touch app/frontend/packs/stylesheets.css
```

And paste in the following, straight from the [tailwind docs](https://next.tailwindcss.com/docs/installation#step-2-add-tailwind-to-your-css)

```css
@tailwind base;

@tailwind components;

@tailwind utilities;
```

And add the following line in `app/frontend/packs/application.js`

```js
import './stylesheets.css'
```

The last step is to tell Rails to use our packs. In `app/views/layouts/application.html.erb`, change

```erb
<%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
<%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
```

to

```erb
<%= stylesheet_pack_tag 'stylesheets', 'data-turbolinks-track': 'reload' %>
<%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
```

*Insert image*

Tailwind should now be working so lets tweak our views to see some Tailwind goodness.

In `app/views/layouts/application.html.erb` change

```erb
<body>
  <%= yield %>
</body>
```

to

```erb
<body class="min-h-screen bg-gray-100">
  <div class="container mx-auto">
    <%= yield %>
  </div>
</body>
```

and in `app/views/home/index.html.erb` change

```erb
<h1>Home#index</h1>
<p>Find me in app/views/home/index.html.erb</p>
```

to

```erb
<section class="py-8 text-center">
  <h1 class="text-5xl mb-2">Ruby on Rails + TailwindCSS</h1>
  <p class="text-xl mb-8">❤️ A match made in heaven️️ ❤️</p>
  <a href="https://next.tailwindcss.com" class="bg-teal-500 hover:bg-teal-700 text-white font-bold py-4 px-8 rounded">Tailwind Docs</a>
</section>
```

You should now see the following page when you navigate to `localhost:3000`

*Insert image*

And now you have TailwindCSS working in your Rails app. Happy coding!
