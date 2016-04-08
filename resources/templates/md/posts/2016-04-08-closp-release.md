{:title "Closp 0.1.28 and closp-crud 0.2.0 Release"
 :layout :post
 :tags  ["clojure" "clojurescript" "closp"]}
 

## CLOSP

A leiningen template combining several useful libraries for clojure and clojurescript web development: 
<https://github.com/sveri/closp>

Due to the work on a graphical interface for closp-crud there is a breaking change:  
Every selmer template has add the `templates` folder now like this:

    {% extends "templates/base.html" %}


## CLOSP-CRUD

### Breaking Change

I completely threw away the idea of using UUIDs as database identifiers. I thought long and hard about that and
finally came to the conclusion that the net gain was not worth the confusion it brought.  
So instead closp-crud generates the database table as defined in the entity definition. 

### Other Changes

* Closp-crud switched from providing core.typed annotations to using plumatic schema instead.
* Fixed some bugs
