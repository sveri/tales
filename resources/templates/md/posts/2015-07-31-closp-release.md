{:title "Closp Release 0.1.20"
 :layout :post
 :tags  ["clojure" "clojurescript" "closp"]}
 

# CLOSP

A leiningen template combining several useful libraries for clojure and clojurescript web development: 
<https://github.com/sveri/closp>

There were several releases between the last announcement and this one, some of which had known issues in the
administrator view. So for this release I concentrated mainly on stabilization by adding several integration tests
for the user and administration workflow.

Other notable features:  

* Internationalization support with tower (starting to translate some of closps strings)
* Adding test2junit to generate junit test results
* Rename `db.users` to `db.user`
* Recaptcha configuration now available in closp.edn
* Providing react.js (0.12.1) and jquery (2.0.3) as local files instead of cdn provided (FIX #6)
* Updating dependencies
  * [clojure "1.7.0"]  
  * [ring "1.4.0"]  
  * [compojure "1.4.0"]  
  * [selmer "0.8.5"]
  * [buddy/buddy-auth "0.6.0"]  
  * [buddy/buddy-hashers "0.6.0"]  
  * [korma "0.4.2"]   
  * [org.xerial/sqlite-jdbc "3.8.10.1"]  
  * [datascript "0.11.6"]  
  * [ring/ring-devel "1.4.0"]  
  * [pjstadig/humane-test-output "0.7.0"]  
  * [closp-crud "0.1.3"]
  * [bootstrap "3.35"]