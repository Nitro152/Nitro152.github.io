---
layout: post
title:      "Active Record Sinatra Project"
date:       2017-10-09 04:14:48 +0000
permalink:  active_record_sinatra_project
---

## Model

### DB design/tables.
Initial thought was to have users, collections, pens, inks and nibs. For the scope of the project I thought that up to inks would be good enough. Also, it's not often, that someone swaps out the nib of their pens.

I used QuickDBD to layout my design. This was the initial database I had when I started the project:
![](https://i.imgur.com/mUizkui.png)

I used the rake utilities that came with Sinatra/activerecord to create migrations for the creation of the tables.

### Associations
I created the classes for each model and added the appropriate associations for each class.
From the initial db design, I had has_many through associations to link the user to pens and inks.
```   
has_many :collections
has_many :pens, through: :collections
has_many :inks, through: :pens
```
As I continued to code I found that these associations were limiting and I was having a hard time with separating objects created by each user. An example is with the pens. If I assigned a pen to a collection for user 1, I could not prevent user 2 from seeing this pen and assigning it to one of their collections. Pens did not have a column for the user_id. The only wat to get to the user_id was to get it through the assigned collection. I tried to get around this by using the has_one association, but this did not work right either.
```
class Pen < ActiveRecord::Base
    belongs_to :collection
    belongs_to :ink
    has_one :user, through: :collection
```
 
### Changes
I decided to give in and update my database design and add user_id references to the pen and ink tables.
![Change to DB diagram](https://i.imgur.com/cwmUBTv.png)
I used migrations again to update the database and make changes to the associations for each model.
```
has_many :collections
has_many :pens
has_many :inks
```
I did not need the through association, and this made it so much easier to keep each user's objects separated.
 
Code shows some additional references that I thought that I would need but ultimately did not use.
 
 
## Controllers
Creation of the controllers was pretty straight forward.
The main Application controller just has the route to the index along with the helper methods for logged_in and current_user.
 
I started with the collections controllers and added the appropriate routes for each of the views.
After creating the first controller, the other controllers were easy because they followed pretty much the same pattern as  the collections controller.

The controllers make use of the get, post, patch, and delete routes. I am also using rack-flash for error messages for incorrect user input.
 
## View
The views were also pretty straight forward. Instead of creating a different view for each function, such as edit and delete, I put two separate forms in the show page to handle the edit and the delete. Each form's action points to the correct route in the controller.

In the create new. There are checkboxes or radio buttons used to assign collections, pens, or inks. The input is validated by the controllers and will edit or create each object according to the selected values.


## After thoughts
There's one thing that is bothering me about using slugs and names. Routes are using the name to direct to objects. If I create multiple objects with the same name, only the first result found will be displayed. I tested this with inks. I made two inks with the same name. Initially I was directed to the ink's show page which was correct, but when I went back to the list of inks. Whether I chose the first or second instance, I am being directed to only the first instance's page. The quickest fix would probably be to use the ID's instead of the name. This is ensured to be distinct.

