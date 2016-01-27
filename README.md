##Ruby Spree Gem

<p>Spree is a complete open source e-commerce solution built with Ruby on Rails. Spree actually consists of several different gems, each of which are maintained in a single repository and documented in a single set of online documentation. By requiring the Spree gem you automatically require all of the necessary gem dependencies which are:</p>

* spree_api (RESTful API)
* spree_frontend (User-facing components)
* spree_backend (Admin area)
* spree_cmd (Command-line tools)
* spree_core (Models & Mailers, the basic components of Spree that it can't run without)
* spree_sample (Sample data)

<p>All of the gems are designed to work together to provide a fully functional e-commerce platform. It is also possible, however, to use only the pieces you are interested in. For example, you could use just the barebones spree_core gem and perhaps combine it with your own custom backend admin instead of using spree_api.</p>

#### Step 1
<p>Prerequisites<p>

```
$ gem install rails -v 4.2.0  
$ gem install bundler
$ brew install imagemagick
$ gem install spree_cmd
```

#### Step 2
<p>Create Rails app and add Spree gem to it<p>

```
$ rails _4.2.0_ new mystore
$ cd mystore
$ spree install --auto-accept
```

#### Problem #1
* These steps give you a functioning e-commerce prototype, but all the files with the code you need to customize are hidden...
* Trying to find exactly how to target specific elements gets tricky

#### Solution #1
* Using [Deface](https://github.com/spree/deface) is one way to extend or replace any view within a Spree store

```
$ touch app/overrides/my_override.rb
```

* Replaces all instances of h1 in the posts/_form.html.erb partial with < h1 > New Post < / h1 >

```
Deface::Override.new(:virtual_path => "posts/_form", 
                     :name => "example-1", 
                     :replace => "h1", 
                     :text => "<h1>New Post</h1>")
```


#### Problem #2
* Solution #1 didn't work. It didn't change anything on the page. Trying all the alternatives in the Spree Deface docs also didn't help or work.

#### Solution #2

* My good friend [Stackoverflow](http://stackoverflow.com/questions/21208300/how-to-override-change-lay-out-of-ruby-on-rails-spree-app-after-installing-boots) had some promising answers.

```
$ touch app/views/spree/layouts/spree_application.html.erb
```
* Copy & paste the following content to the file you just created. It will override the default application.html.erb and manually create a similar design.

```
<!doctype html>
<!--[if lt IE 7 ]> <html class="ie ie6" lang="<%= I18n.locale %>"> <![endif]-->
<!--[if IE 7 ]>    <html class="ie ie7" lang="<%= I18n.locale %>"> <![endif]-->
<!--[if IE 8 ]>    <html class="ie ie8" lang="<%= I18n.locale %>"> <![endif]-->
<!--[if IE 9 ]>    <html class="ie ie9" lang="<%= I18n.locale %>"> <![endif]-->
<!--[if gt IE 9]><!--><html lang="<%= I18n.locale %>"><!--<![endif]-->
  <head data-hook="inside_head">
    <%= render :partial => 'spree/shared/head' %>
  </head>
  <body class="<%= body_class %>" id="<%= @body_id || 'default' %>" data-hook="body">

    <div class="container row">

      <%= render :partial => 'spree/shared/header' %>

      <div id="wrapper" class="row-fluid" data-hook>

        <%= breadcrumbs(@taxon) %>

        <div class='row-fluid'>
          <%= render :partial => 'spree/shared/sidebar' if content_for? :sidebar %>

          <div id="content" class="span<%= !content_for?(:sidebar) ? "12" : "8" %>" data-hook>
            <%= flash_messages %>
            <%= yield %>
          </div>
        </div>
      </div>

      <%= render :partial => 'spree/shared/footer' %>

    </div>

    <%= render :partial => 'spree/shared/google_analytics' %>
    <script>
      Spree.api_key = <%= raw(try_spree_current_user.try(:spree_api_key).to_s.inspect) %>;
    </script>
  </body>
</html>
```

####Problem #3
* Customizing this new file (spree_application.html.erb) because all the main elements (i.e navigation bar, side bar, products list) are partials.
* Overriding the default application.html.erb uglifies everything because your app doesn't have spree_bootstrap. However, you can't actually add spree_bootstrap to your Gemfile because everything will break due to depenedencies not all being compatible and updated