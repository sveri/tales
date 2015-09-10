{:title "Strategy pattern in clojure"
 :layout :post
 :tags  ["strategy pattern" "clojure"]}
 
## Disclaimer

This is the first part of an ongoing series. I am currently working through Head First Design Patterns to refresh my
memory and while the examples are mostly in Java I want to take a look at them and see how to code them in clojure.  
That said, most people want to argue that `design patterns` are not mostly not needed in a functional programming 
language. But for me a `design pattern` is an idea or a concept and both of these can be coded in any language and
here I will show how.  
That aside, please be aware that it is often impossible to translate object oriented patterns into a functional 
style, hence, what you see here are approaches or examples that you can use, but only a subset of them. 

## Definition

The Strategy Pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable. Strategy
lets the algorithm vary independently from clients that use it.

## Java Implementation

In the book we have two different kinds of classes: 

1. An abstract `Duck` class with it's concrete implementations: 
  * `MallardDuck`
  * `ReadheadDuck`
  * ...
2. Behaviors `FlyBehavior` and `QuackBehavior` as interfaces with it's concrete implementations:
  * `FlyWithWings`
  * `FlyNoWay`
  * `Quack`
  * `Squeak`
  * `MuteQuack` 
  
The Duck class contains references to the behaviors:
 
* `performQuack`
* `performFly`

A `swim` method with a concrete implementation valid for all subclasses, a `setXXX` Method to change the behavior during 
runtime and finally an abstract `display` method to be defined by the implementors.

## Clojure implementation
    
    ; Two different fly behaviors
    (defn fly-with-wings [] (println "Flying with wings."))
    (defn fly-no-way [] (println "Poor duck cannot fly."))
    
    ; Three different Quack behaviors
    (defn quack-duck [] (println "Duck is Quacking."))
    (defn squeak-duck [] (println "Duck is Squeaking."))
    (defn mute-duck [] (println "Duck is quiet."))
    
    ; Define a relationship, this means that rubber-duck `is-a` duck
    (derive ::rubber-duck ::duck)
    
    ; Define two Duck entities with different flay and quack behaviors
    (def rubber-duck {:type ::rubber-duck :fly-fn fly-no-way :quack-fn quack-duck})
    (def mallard-duck {:type ::mallard-duck :fly-fn fly-with-wings :quack-fn squeak-duck})
    
    ; Swim function, does the same for all ducks
    (defn swim [duck]
      (println (str (:type duck) " is swimming.")))
    
    ; Fly function, accepting a duck entity and calling the defined fly behavior
    (defn fly [duck]
      ((:fly-fn duck)))
    
    ; Quack function, accepting a duck entity and calling the defined fly behavior
    (defn quack [duck]
      ((:quack-fn duck)))
    
    ; Display function, dispatching on the type of the duck and executing different actions
    (defmulti display (fn [duck] (:type duck)))
    
    (defmethod display ::rubber-duck [duck]
      (println "Displaying Rubber Duck."))
    
    (defmethod display ::mallard-duck [duck]
      (println "Displaying Mallard Duck."))
    
    (swim rubber-duck)
    (swim mallard-duck)
    
    (fly rubber-duck)
    (fly mallard-duck)
    
    (quack rubber-duck)
    (quack mallard-duck)
    
    (display rubber-duck)
    (display mallard-duck)
    
    ; Output
    ;
    ;:de.sveri.designpatterns.chapterone.core/rubber-duck is swimming.
    ;:de.sveri.designpatterns.chapterone.core/mallard-duck is swimming.
    ;Poor duck cannot fly.
    ;Flying with wings.
    ;Duck is Quacking.
    ;Duck is Squeaking.
    ;Displaying Rubber Duck.
    ;Displaying Mallard Duck.
    
One aspect of the pattern is that you can change behavior during runtime. To do that you have `setXXX` methods in 
the `Duck` class. In clojure you can do this by using the `assoc` method on a duck entity:

    (def another-rubber-duck (assoc rubber-duck :fly-fn fly-with-wings))
    (fly another-rubber-duck)
    
This creates a new duck entity with a different fly behavior. This is due to the fact that clojure makes use of
immutable data structures.  
That is also the thing that strikes me about the Java example. Changing implementation of a thing during runtime 
surely has it's usages, but I like to avoid using setters as long as possible, wich it is most of the times.  
So the clojure approach is very much approved here.