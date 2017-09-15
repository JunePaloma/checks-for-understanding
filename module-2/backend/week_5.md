1. How do we make flash messages display on a page?
  
      ***In the item view for show or index:
     <%= link_to "Add to cart", carts_path(item_id: item.id), method: :post %>
      When the link for add to cart is clicked, we hit the create method in the cart controller, which triggers the flash message:


    ***In the CartsController create method: 
      flash[:notice] = "You now have #{pluralize(@cart.count_of(item.id), item.title)}."

      ***I can't remember if any of that is connected to what's going on in application.html.erb?

    ***In application.html.erb:
     <% flash.each do |name, msg| %>
          <%= content_tag :div, raw(msg), class: name %>
        <% end %>

2. Where is cart information/temporary information usually stored?

     ***It's stored in a session object(?) - session[:cart] - where the value of :cart will be a hash (set at the model level and stored    locally on the client side(?)). If there's anything in the cart, it will be stored as a key/value pairs of :item_id => quantity 

     ***Note - I still don't undersand why we don't need a New action in the cart controller; seems like we just push it through at the model level it, via the application controller:

      in application controller:
      def set_cart
        @cart ||= Cart.new(session[:cart])
      end


      in cart model:
      def initialize(initial_contents)
        @contents = initial_contents || {}
      end

      in carts_controller:

      def create
        item = Item.find(params[:item_id])
        @cart.add_item(item.id)
        session[:cart] = @cart.contents
        flash[:notice] = "You now have #{pluralize(@cart.count_of(item.id), item.title)}."
        redirect_back(fallback_location: root_path)
      end

     ***I can't tell what order these things are happening in; it seems like the application controller is calling the model to initilaize, and then only when an item is added do we hit the cart-controller, which isn't really creating a cart so much as adding to the empty hash that we've already created. 
  

3. What might be some reasons not to store cart in our database? Are there any reasons why we would want to persist that information?

    ***We don't want to fill up our database with orphaned almost-orders, we just want actual orders. 
    We might want to persist this information if we wanted to hold onto the state of the cart after a user had logged out (thus destroying their seession) so all their items would still be available when they log in again. 

4. What is the purpose of the asset pipeline?

    ***It minimizes our server call by bundling our precomiled code together so we don't have to make individual calls accross our application when we want to render our site. 
 
5. Why do we precompile our assets?

    ***Because browsers only understand JS, HTML and CSS, we need a means of translating our code into those langugaes so a browser can render it. The asset pipline (/sprockets) is that tool. 

6. What do each of the following tags do? 

```ruby 
<%= stylesheet_link_tag "application" %> 
<%= javascript_include_tag "application" %>
<%= image_tag "rails.png" %>
```
    ***They look like manifests --- almost? So that's a little confusing. I think they allow us to pull assets out of the assets pipline; the first two would live in application.html.erb, and would give us access to our stylesheet and javascript thoughout our application. The image tag would live in a view, and would allow us to render the image by pulling in out of said pipline, where our images folder lives.

7. What are some of the elements of a great read me? What are some of the benefits of taking the time to update a readme for our project?

  ***Mission Statement, how to get it set up (installed and/or lanunched on a server), notes on API keys or other credentials that the creator needs to give you, end points you can visit, contributors, version control. 
  
  ***Starting with a Read Me can help guide your development if you're committed to a certain user experience; updating it can give you a chance to clarify your intentions. 

8. What are the top four accessibility issues that we as developers should be aware of?

  ***sensory impairments - like color-blindess, blindness, and deafness - that may narrow how people experience site.
  mobility impariments - people may not be able to mouse through our sites, my be relying on syntatical html to navigate pages.
  congnative impairments -  like dyslexia. People may be extra sensitive to flashing/lots of colors/noises or white space between characters. 

9. `before_save` is an example of a what? Where in our Rails application would we find a `before_save`?

***A callback. Callbacks trigger logic before or after executing controller actions. A before_save would live in the model? But we've had occassion to put before actions in, for instance, the application controller to make sure a user is authorized to trigger controller actions (like updating/creating/deleting a resource).

10. Given the following object, how would we create a scope for all users who are active?

```ruby 
User.create(name: "Happy", active: true)
```
 ***I have two different things in my notes, either would live in the routes file: 
 resources :users do 
  collection do
     cats
  end 
 end 
 
 ***or:
 
 ***scope :users do
  resources cats
  end 
 

11. What is the difference between a scope and a class method?

  ***scope can be set in a model to narrow queries; apparently they are class methonds in the background, just limiting the class to scope out WHERE some condition is true. So the difference is syntax?  
