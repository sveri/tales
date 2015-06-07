{:title "Closp Release 0.1.15"
 :layout :post
 :tags  ["clojure" "clojurescript" "closp"]}

I did two releases in the last week, the second one being more a bug fix release. The goal of the last changes was
to eliminate some libraries like piggyback and get a cleaner figwheel experience.  
While I was doing that I also updated to clojure 1.70-RC1 and added a very small cljc example while removing the
cljx support.  

Figwheel needs to be started in a separate REPL now. However, if one needs I guess one could still start it in the
REPL process inside the IDE where the main process is running.

I am still thinking about moving closp to luminus, but just do not have the time for a major change like this 
right now, so, we will see :-)
