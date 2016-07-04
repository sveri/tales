{:title "Phoenix vs Spring Boot vs Sails.js performance comparison"
 :layout :post
 :tags  ["spring boot" "phoenix" "sails.js" "performance" "comparison"]}
 
 Working on a project where I consume an API which I plan to use from 
 browsers for instance I was trying out spring boot. I liked the development 
 speed, the documentation and everything in general.  
 However, I was wondering how fast it is, given that I want fast user feedback.
Searching for speed and frameworks you will naturally find a link to phoenix
any time soon.  

So what I did was to 

1. Build my application based on spring boot
2. Build an elixir api with one rest endpoint targeting the same database
3. Build a sails.js api with one rest endpoint targeting the same database

From an application perspective sails.js and phoenix are almost the same
as I just started out liked described in the docs and only added that one 
rest api endpoint.  

Spring boot is a bit different as I also provide login / register and some templates.  
For the comparison purpose I also added that one rest endpoint without having to login.

All three applications offer this endpoint: `/api/browserlink` which uses
the internal model layer:

1. Spring boot - Hibernate
2. Elixir - Ecto
3. Sails.js - Waterlin

so, whatever comes with the framework.

Also all three applications connect to the same database and query the 