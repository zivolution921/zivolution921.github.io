---
layout: post
title:  "Rails Subscription Application"
date:   2016-12-14 14:57:11 +0000
---


Created subscription application with 3 kinds of monthly subscriptions, each one has certain amount of boxes with items that will be shipped to the user.
Understanding RESTFUL ROUTES in Rails:
from the browser we are sending HTTP request containing HTTP verb such as POST or GET, that hits specific URL path. the router in the application process the request and route or matches to the proper controller action. Than, the controller action either perform the task such as creating or updating, or deleting a record from the database.

in this example you can see by inspecting in the browser the action which is the destination is /users goes to my route and send POST through Restful routes for HTTP verb Post. The action /users match users route and creating a resource.
def create
 @user = User.new(user_params)
 if @user.save
 session[:user_id] = @user.id
 redirect_to root_path, notice: “Thanks for signing up!”
 else
 render :new
 end
 end
in the users_controller we have the action create that instantiating the User model with user_params and storing it in instance variable @user. if the user that is stored is saved into the database. Than we are finding the user from the database using user_id in the sessions hash in order to implement current_user helper method to track the current_user ( session[:user_id] = @user.id). than we are redirecting to root_path which is the home page and sending notification. In case that the user is not saved into the database due to some validation in the user model than we will render new template.
In the application controller i have some methods that could be used in all other controllers since all the controllers are inheriting from the application controller. we have a method curent_user to track the current user that is signed in.

this method will find if there is user_id in the session hash, if it exists than we will check if we have current_user and if not than we going to assign @current_user = User.find(session[:user_id]) to find the user from database using user_id and assigning it to instance variable current user.
Box Model

The role of model is to provide access to application data and take care of all the business logic. the model inherits from ActiveRecord::Base to connect the database migrations file as instructions to modify your database.
Some of the business logics in the Box model has_many items and belongs_to plan. I created item_attributes method which is a custom writer in order to attach items to specific box. The setter method is called whenever the box is initialized with item attributes.
the item_attributes is being called in the create action bellow.

we are instantiating box for specific plan with box_params. if the box is saved into the database we are going to have array of hash with values of the item_attributes that we getting from the form, stored in local variable item_attrs and we will pass the array of hash argument into @box.item_attributes.

Before creating box or calling any of the actions in the box_controller we have a filter method before_action :set_plan that will be called. In this method we are looking if there is plan already existed we want to attach that plan to the box. In one condition where we have nested routes, for example: http://localhost:3000/plans/1/boxes/1/items/new
In this condition we will find the plan from the database using plan_id and assigned to @plan instance variable. In the other condition we do Not have nested route under plan for example this URL: http://localhost:3000/boxes/new we will choose from dropdown one of the plan that we want to associate to the box.
I have nested resources for boxes and also not nested resources that the admin could create either one, here is the nested resource:
resources :plans do
 resources :boxes do
 resources :items
 put ‘ship’ => ‘boxes#ship’
 end
 end
the boxes in nested under plan and the items are nested under the boxes.
the other condition I have just the boxes resource not nested: resources :boxes
