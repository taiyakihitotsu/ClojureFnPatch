## Abstruct
This patch block to compile these functions below:

`(fn [a r g s])`
`(fn ([a r g s]))`
`(defn name [a r g s])`
`(defn name ([a r g s]))`

and force you to write a body or `nil`.
(In the Clojure Compiler, they return `nil`.)



## Reason
* `(fn [arg])` which has no body is used to return nil at any time but there's `(constantly nil)` for it.
* On the other hand, it would be difficult (and I stucked in) to fix bugs about applying `~@` to `'()` in a `defmacro` clause with a careless oversight because this wouldn't spit an error in a couple of cases.



## Example

with this:
```
user=> (defn foo [bar])
Syntax error (IllegalArgumentException) compiling at (REPL:1:1).
([bar]) has no body. fn/defn must have them.

user=> (defn foo [bar] (inc 1))
#'user/foo
```

without (it's normal):
```
user=> (defn foo [bar]) 
#'user/foo
```

with:
```
user=> `(fn [a] ~@(filter even? [1 3 5]))
(clojure.core/fn [user/a])

user=> (defmacro aa [x y z] `(fn [] ~@(filter even? [x y z])))
#'user/aa

user=> (aa 1 3 5)
Syntax error macroexpanding clojure.core/fn at (REPL:1:1).
(fn([])) has no body. fn/defn must have them.

user=> (aa 1 2 3)
#object[user$eval149$fn__150 0x32c8e539 "user$eval149$fn__150@32c8e539"]
```

without:
```
user=> `(fn [a] ~@(filter even? [1 3 5])) 
(clojure.core/fn [user/a])

user=> (defmacro aa [x y z] `(fn [] ~@(filter even? [x y z])))
#'user/aa

user=> (aa 1 3 5)
#object[user$eval148$fn__149 0x70efb718 "user$eval148$fn__149@70efb718"]

user=> (aa 1 2 3)
#object[user$eval152$fn__153 0x4e70a728 "user$eval152$fn__153@4e70a728"]

```



## How to use
* Patching
  1. `git clone https://github.com/taiyakihitotsu/ClojureFnPatch`
  2. `cd ./ClojureFnPatch`
  3. `git clone https://github.com/clojure/clojure.git`
  4. `patch -d clojure -p1 < defnfix-patch-proto.patch`
* Tests 
  1. `cd ./clojure`
  2. `mvn clean test`
* Repl
  1. `mvn -Plocal -Dmaven.test.skip=true package`
  2. `java -jar clojure.jar`



## Confirmed Version
Clojure - 1.12.0-master-SNAPSHOT




## Author
Taiyaki



## License
EPL
(I mean the same of Clojure using)
