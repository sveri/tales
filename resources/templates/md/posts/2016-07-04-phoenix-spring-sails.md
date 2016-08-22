{:title "Phoenix vs Spring Boot vs Sails.js performance comparison"
 :layout :post
 :tags  ["spring boot" "phoenix" "sails.js" "performance" "comparison"]}
 
 **Update**
 
 Please consider these numbers to be flawed in different ways.
 
 1. I was doing the tests using a WiFi connection which has no stable speed in it self.
 I thought it was ok to do so, but it turns out that it is not.  
 Using a WiFi you will get different numbers from run to run just because of that.
 2. The Spring Boot application was running behind a HAProxy while the Elixir and Sails.js application
 were not. That alone was causing like 150ms difference.  
 Lesson learned, HAProxy alone will will add a lot of latency to your application at least
 in the very basic configuration that I used. Maybe it is possible to tune it, but thats
 a different topic.
 
 I made some more tests on a stable connection without the HAProxy and compared Spring
 Boot with Ecto and it turns out that there were not that much differences, usually between
 1 and 2 ms in my case. Anyone wanting to know better, have a look here: <https://www.techempower.com/benchmarks/>. 
 
 
 I will leave the old text as a reminder that, no matter how experienced you are, always
 question yourself and never forget, everyone makes mistakes.
 
 

**Old Entry**
 
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
as I just started out like described in the docs and only added that one 
rest api endpoint.  

Spring boot is a bit different as I also provide login / register and some templates.  
For the comparison purpose I also added that one rest endpoint without having to login.  
**Update** Please note that while the spring boot controller contains an endpoint
to persist data I did not use that during the test.

All three applications offer this endpoint: `/api/browserlink` which uses
the internal model layer:

1. Spring boot - Hibernate
2. Elixir - Ecto
3. Sails.js - Waterlin

so, whatever comes with the framework.

Also all three applications connect to the same database and query the same
table, whereas the call is limited to 50.  
I made it so that I can leave out the database as a parameter.

## Setup

The applications run in docker container. I looked at dockerhub and picked up
the appropriate ones by looking which exist for java, elixir and node js.  
So most probably they run within different OS (debian / ubuntu).  
But the containers run on the same hardware which is a i3770 and 16 GB RAM.

I looked for the production setup instructions and applied them as written
in the documentation, no further fine tuning was done.

## Results

Numbers are in milliseconds if not noted otherwise.

### Phoenix

```
Thread - Repetitions      Mean       Median      Error  

1 - 1000                  74         68          0
10 - 100                  120        105         0
100 - 10                  905        622         0
1000 - 10                 6883       4397        22.19%
```

Here are the complete results: <a href="/img/elixir.png" target="_blank">Elixir</a>

### Sails.js

```
Thread - Repetitions      Mean       Median      Error  

1 - 1000                  136        125         0
10 - 100                  158        141         0
100 - 10                  1003       795         0
1000 - 10                 8321       4685        6.72%
```

Here are the complete results: <a href="/img/sailsjs.png" target="_blank">Sailsjs</a>

### Spring Boot

```
Thread - Repetitions      Mean       Median      Error  

1 - 1000                  196        188         0
10 - 100                  250        246         0
100 - 10                  2579       2313        0
1000 - 10                 7631       5712        60.87%
```

Here are the complete results: <a href="/img/sailsjs.png" target="_blank">Spring Boot</a>

What surprised me the most is that spring boot has the worst performance here.

I expected elixir to be the fastest, having read so much about its speed, but that
sailsjs comes close, especially with 10 and 100 concurrent connections was another suprise. 

On the other hand I was watching the CPU saturation during the tests and I never
reached full saturation.

Somehow when using 1000 concurrent connections I get a lot of drops. I am not sure what exactly
the problem here is, but I wont investigate further for now.  
I guess if I resolved these problems I would get some better numbers
fully utilizing the CPU.

### Code

These are the commits to the projects that I ran the tests on

* Spring Boot: <https://github.com/sveri/historify-spring-backend/tree/f890af4d76c85b7b57aad770d599c2a83fec18be>
* Phoenix: <https://github.com/sveri/phoenix-historify-api/tree/b510b5a744720c74e007723e2ce89576b5619dc9>
* Sailsjs: <https://github.com/sveri/sailsjs-historify-api/tree/a74001dc50919728e236ff8c2274486d28da9d36>

Whereas the relevant api entrypoints can be found here:

* Spring Boot: <https://github.com/sveri/historify-spring-backend/blob/f890af4d76c85b7b57aad770d599c2a83fec18be/src/main/java/de/sveri/historify/controller/rest/HistoryApi.java>
* Phoenix: <https://github.com/sveri/phoenix-historify-api/blob/b510b5a744720c74e007723e2ce89576b5619dc9/web/controllers/browser_link_controller.ex>
* Sailsjs: <https://github.com/sveri/sailsjs-historify-api/blob/a74001dc50919728e236ff8c2274486d28da9d36/api/controllers/BrowserLinkController.js>

If anybody has any tips on improving the performance I am happy to rerun the tests
and update the results / code.