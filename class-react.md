## TL;DR

### Article Link
github.com/roman01la/uix (See uix.recipes.server-rendering for ssr in JVM) -- can be used with rum and refrankenstein
https://gist.github.com/roman01la/85b7a473f5a5d48612c99d9bc6dc4f03
https://github.com/reagent-project/reagent-server-rendering/

### Author

## Key Takeaways
* Uses UIX for JVM ssr
* Possibly rum and refrankenstein for routing

## Useful Code Snippets
Example of a project
```clojure
(ns foo.core
  (:require [uix.core.alpha :refer [defui]]))

(defui main [class]
  [:main {:class class}
    [:p "Hello!"]])
(ns foo.bar
  (:require [uix.core.alpha :refer [defui]]))

(defui sidebar [class]
  [:aside {:class class}
    [:p "Hello!"]])

```
Macro for leveraging the constants table in clojurescript to embed directly into the react's virtual DOM
```clojure
(defn register-constant! [env val]
  (let [id (gen-constant-id val)]
    (swap! env/*compiler*
           (fn [cenv]
             (-> cenv
                 (update-in [::ana/constant-table] #(if (get % val) % (assoc % val id)))
                 (update-in [::ana/namespaces (-> env :ns :name) ::ana/constants]
                            (fn [{:keys [seen order] :or {seen #{} order []} :as constants}]
                              (cond-> constants
                                      (not (contains? seen val))
                                      (assoc :seen (conj seen val)
                                             :order (conj order val))))))))
    id))
    
(do (cljsc/emits "cljs.core." (cljsc/munge value) " = ")
    (-> sym meta :ast cljsc/emits))
```
## Useful Tools
* Hiccup
* Re-frankenstein/Rum

## Comments/ Questions
