Rails is a web framework within the Ruby programming language and it is also a Ruby gem. Ruby gems are comprised of the following components: code, documentation, and a gemspec. The gemspec contains the information of what the gem does and who created it. And since the gemspec is also Ruby code, you can wrap scripts to generate file names. Gems make it easy for Ruby programmers to share code that they want to install in their applications, and it is this process which makes Ruby on Rails one of the most popular ways to build great web apps.
The Rails framework uses the MVC architecture for the arranging of all your code for a Ruby web app. The controller is usually responsible for taking important information from the models and then sending that data to the views for rendering, although not always—you can use Rails to generate static HTML views, and you can also have a controller action generate a view with no corresponding model. 



You can also use Bundler to create your own gems and also to handle gem dependency management.
Understanding MVC and the Rails render() method
Rails can render templates and files, so it can also render images, raw text, and various different file formats such as html, xml, json, and txt. But we can also add more options such as csv or pdf to the Rails built-in render() method that comes packaged as part of the Rails framework. The render() method is the common interface for rendering a given model or template. 
In this example we’ll look at how you can modify the render() method and return a PDF created with the Prawn gem—essentially, creating a Rails plugin that is a PDF renderer. 
Note: A renderer is really a hook exposed by the render() method to customize its behavior. For further documentation about the Prawn gem, here is the Github repository for Prawn that you may check out:
https://github.com/prawnpdf/prawn
Since Rails generators include a generator for writing plugins, creating the plugin that will be the PDF renderer is easy. You can create plugins for extending the Rails framework. For our PDF renderer example, hop into your terminal and run the command rails plugin new pdf_renderer.  
After running that command, go into the directory that your newly generated Rails plugin is in and run the command ls to see a listing of all the files in your Rails plugin project directory. You’ll see that a basic plugin structure containing a pdf_renderer.gemspec file, a Rakefile, a Gemfile, and lib and test directories were created—just like what happens if you were to run the rails new myapp command to create a new Rails app titled “myapp” which generates the skeletal structure of a new Rails application. One difference that you’ll notice is that in generating the plugin project, there’s a test/dummy directory which allows you to run your tests inside a Rails app environment.
The pdf_renderer.gemspec file that was generated provides basic Ruby gem specification that lists the gem’s author(s), version, gem dependencies, and source files and allows you to bundle the plugin into a Ruby gem—which makes it easy to share your code across different Rails apps.
Just like with a regular Rails app, the little plugin app example for writing a renderer includes a Gemfile in which your gem management can be handled with Bundler, which locks our environment to use only the gems listed in both the pdf_renderer.gemspec file and the Gemfile. This is no different than using only the gems listed in the Gemfile.lock file and the Gemfile of a regular Rails app. New or updated gem dependencies are bundled by running the bundle install and bundle update commands respectively from the terminal while in the plugin app’s root.
Booting the dummy Rails application created inside of the test directory is very similar to the way we boot any other Rails application, except here you boot it by running the rails s command in your terminal from the plugin app’s test/dummy directory inside the plugin project file.
A note here about booting: the boot file, which is located in test/dummy/config/boot.rb, is similar to the boot file in any other Rails app except that this one needs to point to the Gemfile at the root of the plugin project like so:


 You’ll also notice that the test/dummy/config/application.rb file is a stripped-down version of the /config/application.rb file in a regular Rails app as well:


Now, in this example of making a PDF renderer plugin using Prawn, we add the Prawn gem to our Gemfile just like we would in any other Rails app where we might want to use it.
And since the Prawn gem will be a dependency for this Rails plugin example, we also need to add it to the pdf_renderer.gemspec file by adding this line of code, s.add_dependency “prawn”, “0.12.0”, like so:



After doing adding the Prawn gem, you would run the command bundle install just as you would with any other Rails app, then hop into interactive Ruby by running the irb command and create a sample PDF file in irb like so:

 


In your text editor, in your project file directory that was made for writing this plugin, you’ll see a sample PDF file there. In this screencap below of my text editor, you will see two sample PDF files. That’s because when I created the first one, sample.pdf, forgot to screencap the irb session so I made a second sample PDF file for illustrative purposes so you could see what the terminal output should look like for the irb session.

A word about Prawn here: If you read the documentation, you will see that Prawn has its own syntax for creating PDF files. It gives us an API that is very easy to use. However, what it cannot do is convert HTML files into PDF files. If you want to be able to do that, and you cannot find a Ruby gem that will be able to handle that task, you may want to consider trying your hand at building your own Ruby gem using Bundler in order to accomplish that task. But you can always check out what gems are available by going to Rubygems.org and looking for a gem that already does it that someone else built. There are tons of new Ruby gems being built and uploaded to Rubygems.org every day so there is a high probability of finding one that will meet your app’s needs.
Below is the terminal output of the test results for testing the code for the plugin. As with any other Rails project, you’ll want to write at least a couple of sanity tests. The tests and the code for this PDF renderer plugin are rather simple and straightforward. 

Make a test file for your integration tests: /test/integration/pdf_delivery_test.rb and write a couple of simple integration tests like so:


Now we write the first test to verify that a PDF file is being returned when booting up the app by going to localhost: 3000/home.pdf with the expectation it should fail until we write the code for telling Rails how to handle the pdf option in the render() method inside of the /lib/pdf_renderer.rb file like so:

You will also need to make a view file, index.pdf.erb that corresponds to the index action that you should have written as part of your basic CRUD methods in your dummy app’s controller. This is very similar to what we do when we create the index.html.erb file in other Rails applications. Of course, you’ll want to add the appropriate routes, too:




Now if run your rake tests you can have fun watching them pass.



To see this in your browser, go to the test/dummy directory and start up the rails server and then go to localhost: 3000/home.pdf. You should see a PDF form pop up that you can edit. Here is a screencap of mine:

Now, you’re probably wondering how Rails “knew” what content type to set in the return or response to our request. The answer is simple. Rails comes with a pre-packaged set of registered formats and MIME types. So Rails “knows” what kind of content type to set in a response. You may verify this by examining the heart of the Rails framework in rails/actionpack/lib/action_dispatch/http/mime_types.rb. There is where you will find a listing of all the content types that Rails comes pre-packaged with to be able to handle this for us: png, jpg, gif, css, html, text, js, bmp, tiff, ics, csv, mpeg, xml, yaml, atom, json, zip and pdf. You may verify this by checking out the Github repository for Rails here: https://github.com/rails/rails/blob/master/actionpack/lib/action_dispatch/http/mime_type.rb 
If you take a moment to go over that repository’s code, you will see that the PDF format is definted with its specific content type. When the user (in this case, us) makes a request in the browser to see /home.pdf URL in localhost, Rails retrieved the PDf format from the URL and then verified it with the format.pdf code block that was defined with the index method (or action) in the HomeController, and then proceeded to set up the correct content type before invoking the block that called the render() method. 
Now, the key word here to understanding the Rails rendering stack is the word “render.” Whenever you see the word “render”, you should associate it with everything that has to do with what a user sees when making a browser request—everything from interactive forms to autoresponders to views. 
Rails also relies on its rendering stack to be able to extend ActionController and ActionMailer within the Rails framework as well, which will be treated in future articles in this series. Both ActionController and ActionMailer share several common features and in accordance with the DRY principle, the underlying code governing those shared features was abstracted out to the AbstractController class, a convenient public superclass which ActionController and ActionMailer both use and inherit from. 
The way AbstractController was written, you can pick and choose which kind of functionalities you want. One of its included modules is ActionView. One of the instances of ActionView::Base is view_context. It is an instance of a View class, and that View class must have the following methods: View.new[lookup_context, assigns, controller], according to the API documentation. It is the context in which templates in your project are evaluated. When instantiated, it receives the view_assigns() method as one of its parameters which should return a hash. To break it down a little better, think of it this way: The reason the link_to method works in a template, is because it’s a method that is available inside ActionView::Base. 

What assigns does is it references all of the instance variables in the controller that will be accessible in the view. This is why whenever you declare an instance variable in a controller, such as for example, @products = Product.all, where @product is designated as an assign and as such it will also be available in your views. Now, if you do not want a controller sending any assigns to the view, you just simply override the view_assigns() method to return an empty hash. In returning an empty hash, you will not have any of the controller methods (also known as actions) passing to the view.
The way the Rails rendering stack is set up, it allows developers to hook into the rendering process and customize it with their own features. This is an awesome amount of power to have at your disposal. And with all that said, bear in mind that with great power also comes great responsibility. So use it wisely and always be mindful that you’re not exposing yourself, or even worse, exposing your clients or your employer, to any potential security holes. So the first question you should ask regarding customizing the Rails rendering process when architecting your app is “How much do I really need to extend the rendering stack?”












