# RAILS MUST KNOW

## 1\. How it works

### Rails works in a MVC architecture.

A model is the data, and how you get it. The controller is what makes you application flow. And the view is how you communicate with the controller. Rails uses routes to get from you browser pages to controller methods.

### You must have:

- A route for each view.
- A view must be related with some controller.
- The route must match with a controller action.

## 3\. Install and run

Install it from [here](http://installrails.com/). If you are using linux I recommned installing it using the rbenv method.

Create a new prohect and start a server on port 3000:

```
$ rails new project_name
$ cd project_name
$ rails server
```

Add gems. Go to the Gemfile and add the gem information to it. Install them and restart the server:

```
$ bundle install
$ rails server
```

## 4\. Models

A model is an abstraction of how your data works. It is made of a database and Ruby object. The object controls you database and is accessible from your controller.

To create a model run:

```
$ rails generate model name_of_the_model prm1:prm_type1 prm2:prm_type2 ...
$ rake db:migrate
```

Where prm is a property of your model (table) and prm_type os the type of the property. The second line migrates the model to your database.

## 5\. Controllers

The controller will contain the logic of the app. It will operate the models and respond to the view actions.

To create a controller:

```
$rails generate controller controller_name
```

To create a controller with a view:

```
$ rails generate controller controller_name_goes_here view_name_goes_here
```

## 6\. Views

A view is always related to some controller. A view is is always a something.html.erb or something.html.haml file inside of app/views/correspondent_controller_name folder. This will render data and interact with users. A view must a have a route for it.

## 7\. Routes

Routes define how views are accessed from the browser and also which method should respond to it. It is how the decode the URL into commands. You can manage them in the config/routes.rb file and view the routing configuration by running:

```
$ rake routes
```

## 8\. Rails objects

- ### Model objects (Active records):

  Query and manage the database.

- ### params:

  #### 1\. User supplied parameters

  GET (<http://domain.com/url?param1=value1&param2=value2> will set params[:param1] and params[:param2]) POST (e.g. JSON, XML will automatically be parsed and stored in params) Note: By default, Rails duplicates the user supplied parameters and stores them in params[:user] if in UsersController, can be changed with wrap_parameters setting

  #### 2\. Routing parameters

  match '/user/:id' in routes.rb will set params[:id]

  #### 3.Default parameters

  params[:controller] and params[:action] are always available and contains the current controller and action<br>

  #### Common usage:

  fetch data from model. Examples:<br>

  ```
  Doc.find(params[:id])
  Doc.find(params.require(:book).permit(:title, :author));
  ```

- ### Instace variables (@variables):

  They are available to methods of the class and also for the view.

## 9\. Types for Model Fields

- **String:** Limited to 255 characters (depending on DBMS) Use for short text fields (names, emails, etc)
- **Text:** Unlimited length (depending on DBMS) Use for comments, blog posts, etc. General rule of thumb: if it's captured via textarea, use Text. For input using textfields, use string.
- **Integer:** Whole numbers
- **Float:** Decimal numbers stored with floating point precision Precision is fixed, which can be problematic for some calculations; generally no good for math operations due to inaccurate rounding.
- **Decimal:** Decimal numbers stored with precision that varies according to what is needed by your calculations; use these for math that needs to be accurate See this post for examples and an in-depth explanation on the differences between floats and decimals.
- **Boolean:** Use to store true/false attributes (i.e. things that only have two states, like on/off)
- **Binary:** Use to store images, movies, and other files in their original, raw format in chunks of data called blobs
- **:primary_key** This datatype is a placeholder that Rails translates into whatever primary key datatype your database of choice requires (i.e. serial primary key in postgreSQL). Its use is somewhat complicated and not recommended. Use model and migration constraints (like validates_uniqueness_of and add_index with the :unique => true option) instead to simulate primary key functionality on one of your own fields.
- **Date:** Stores only a date (year, month, day)
- **Time:** Stores only a time (hours, minutes, seconds)
- **DateTime:** Stores both date and time
- **Timestamp:** Stores both date and time Note: For the purposes of Rails, both Timestamp and DateTime mean the same thing (use either type to store both date and time). For the TL;DR description of why both exist, read the bottom paragraph.

## 10\. Nice to have GEMs

- haml
- devise
- simple_form
- paperclip
- masonry-rails
