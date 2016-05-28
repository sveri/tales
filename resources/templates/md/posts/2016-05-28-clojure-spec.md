{:title "Clojure Spec in Closp Crud"
 :layout :post
 :tags  ["closp-crud" "clojure" "clojure.spec"]}
 
This week Rich Hickey announced the release of clojure.spec which is a new namespace in the clojure library. It
aims to provide runtime contracts for clojure.

There is already a lot of great documentation available:

* About: http://clojure.org/about/spec
* Guide: http://clojure.org/guides/spec 
* API: http://clojure.github.io/clojure/branch-master/clojure.spec-api.html

clojure.spec is part of the 1.9 release, of which currently 1.9.0-alpha3 
is available. There is no timeframe of a final 1.9 release yet, so it 
is all in a very early stage. However, considering this, the documentation is ready for primetime, 
at least from my point of view.

To try things out, I took my crud library and converted it from schema to
clojure.spec. The transition went pretty smooth with two problems I encountered.

The first one was a bug which is already fixed in alpha-3, so I wont go
into detail here.

The second is a bit tougher one, at least for me. Consider this example:

```
(s/def ::foo string?)

(s/def ::column (s/keys :req-un [::foo]))

(s/def ::columns (s/cat :col ::column))

(s/fdef test-fn :args (s/cat :cols ::columns) :ret ::columns)
(defn test-fn [cols]
  (vec (remove #(= true (:autoinc %)) cols)))


(s/instrument-all)
```

Running this: `(test-fn [{:foo "bar"}])` will result in this error:  

```clojure
ExceptionInfo Call to #'de.sveri.clospcrud.s-play/test-fn did not conform to spec:
In: [0] val: [{:foo "bar"}] fails spec: :de.sveri.clospcrud.s-play/column at: [:args :cols :col] predicate: map?
:clojure.spec/args  ([{:foo "bar"}])
  clojure.core/ex-info (core.clj:4617)
```

This error comes in different forms and hit me a few times: 
```clojure
In: [0] val: ([{:name "age", :type :int, :null false}]) fails at: [:args] predicate: (cat :cols :de.sveri.clospcrud.schema/columns),  Extra input
:clojure.spec/args  ([{:name "age", :type :int, :null false}])
  clojure.core/ex-info (core.clj:4617)
```

The reason is that you will have to wrap every `seq` into `(s/spec ...)`.
At least thats my understanding, the docs talk about regex here and this is 
where a personal brain disconnect comes from, which made it harder for me
to recognize and fix the problem: <http://clojure.org/guides/spec#_sequences>.
If you scroll down a bit you will find the explanation.

Long story short, the solution is this:

```
(s/def ::foo string?)

(s/def ::column (s/keys :req-un [::foo]))

(s/def ::columns (s/cat :col ::column))

(s/fdef test-fn :args (s/cat :cols (s/spec ::columns)) :ret ::columns)
;                                  ^ Note the s/spec above

(defn test-fn [cols]
  (vec (remove #(= true (:autoinc %)) cols)))


(s/instrument-all)
```

That said all in all I am very pleased so far. I always dreamed of a 
official `schema` library, and would not have thought it to come true so soon.  

Special thanks to the cognitect team and everybody else working on that feature.


Here is some of my code that I switched from schema to spec:


```
; schema
(def liqui-column [(s/one s/Keyword "name")
                   (s/one (s/cond-pre s/Keyword [(s/one s/Keyword "texttype") 
                   (s/one s/Num "size")]) "type")
                   (s/optional s/Keyword "attr1-type")
                   (s/optional s/Any "attr1-value")
                   s/Any])

(def liqui-columns [liqui-column])

(def liqui-entity-description {:name Str :columns liqui-columns})

;;clojure.spec

(s/def ::name string?)

(s/def ::column (s/cat :keyword keyword?
                       :type (s/alt :keyword keyword?
                                    :varchar (s/spec (s/cat :keyword keyword? 
                                    :size number?)))
                       :first-attr-type (s/? keyword?)
                       :first-attr-value (s/? ::s/any)
                       :rest (s/* ::s/any)))

(s/def ::columns (s/cat :column (s/+ (s/spec ::column))))

(s/def ::entity-description (s/keys :req-un [::name ::columns]))
```

And:

```
;schema
(def column-types (s/enum :int :varchar :boolean :text :time :date
                          :char :binary :smallint :bigint :decimal
                          :float :double :real :timestamp))

(def column {:name                        Str :type column-types
             (s/optional-key :null)       Bool
             (s/optional-key :max-length) Num
             (s/optional-key :required)   Bool
             (s/optional-key :pk)         Bool
             (s/optional-key :autoinc)    Bool
             (s/optional-key :unique)     Bool
             (s/optional-key :default)    Any
             (s/optional-key :refs)       Str
             (s/optional-key :fkname)     Str})

(def columns [column])

(def entity-description {:name Str :columns columns})

;;clojure.spec
(s/def ::name string?)

(s/def ::entityname string?)
(s/def ::entity-ns string?)
(s/def ::ns string?)
(s/def ::colname string?)
(s/def ::colname-fn string?)
(s/def ::ds-column (s/keys :req-un [::colname ::colname-fn]))
(s/def ::cols (s/cat :cols (s/+ ::ds-column)))
(s/def ::template-map (s/keys :req-un [::entityname ::entity-ns ::ns ::cols]))

(s/def ::column-types #{:int :varchar :boolean :text :time :date
                        :char :binary :smallint :bigint :decimal
                        :float :double :real :timestamp})


(s/def ::type ::column-types)
(s/def ::null #(instance? Boolean %))
(s/def ::max-length number?)
(s/def ::required #(instance? Boolean %))
(s/def ::pk #(instance? Boolean %))
(s/def ::autoinc #(instance? Boolean %))
(s/def ::unique #(instance? Boolean %))
(s/def ::default ::s/any)
(s/def ::refs string?)
(s/def ::fk-name string?)

(s/def ::column (s/keys :req-un [::name ::type]
                        :opt-un [::null ::max-length ::required ::pk 
                        ::autoinc ::unique ::default
                                 ::refs ::fkname]))
(s/def ::columns (s/cat :column (s/* ::column)))

(s/def ::entity-description (s/keys :req-un [::name ::columns]))
```

As you can see, the declaration itself for such a small spec contains 
more code, this is due to the way maps are defined. The big advantage here
is reuseability. I cannot tell if this comes true or not. For me, I did not
 see it yet, but, its a small library and time will tell.
 
Another big feature is the integration with test.check, so this is on my todo list
for now and you should have a look into it too.


**Update**

I forgot about one thing. Some datatypes are missing, the one that hit me
was `boolean`. I defined it as `#(instance? Boolean %)`. However, one 
drawback is that no generator for this exists.  
Currently there are two solutions:

```
(defn boolean? [x] (instance? Boolean x))
(s/def ::boolean (s/with-gen boolean? #(gen/boolean)))
```   
As it is shown in: <http://clojure.org/guides/spec#_generators>

Or, the solution that I prefer for now:

```
(s/def ::boolean #{true false})
```
Just define it as a set of true and false values.

Both works for generating example datasets.