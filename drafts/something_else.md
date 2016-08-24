# Objects

Do you know of all the different ways you can retire in your country?
Well, there may not be many different ways where you live, but in Brazil we have plenty.
Imagine we're building a system to allow Brazillian citizens to estimate the different possibilities for retirement, and
that we're building it in a functional programming language, Clojure.

I'll not get into the details of the calculations, it should suffice to say that they are rather complex. For the point I'd like to make, 
the important part is that the complexity of the calculation rules are such that we must also deliver a
calculation log along with the result, so the citizen can understand how the system reached it. 

Since logging is such a *[cross-cutting concern](https://en.wikipedia.org/wiki/Cross-cutting_concern)*, 
it is usually unavoidable that the calculation functions will also be responsible for logging.
So, just to illustrate, in this system we would find functions like this one:
```clojure
(defn is-eligible? [citizen]
  (log (str "Eligibility check for " (:name citizen)))
  (let [eligible (>= (age citizen) 65)]
    (if (eligible) 
      (log "Citizen is eligible")
      (log "Citizen is not eligible"))
    eligible))
```

Now, although we have coupled the log function to every business rule function, 
everything should be fine as long as we don't have to deal with the details of logging.
But that is exactly what I want to propose now: 
How would this code change if it was necessary to support more than one kind of logging?

For instance, lets suppose the current `log` function simply writes to standard output. 
Now we want to send these messages through a socket, to a specific listener.
In essence, now the logging functionality has the following tasks:
* Determine which kind of logging has to be done (standard output or socket)
* Write to standard output when it is the case;
* Write to the socket when it is the case, which involves:
  * Connecting to the listener, from an adress and port;
  * Writing to the socket when the connection was successful;
  * Writing and error to standard ouput when no connection could be made.

To support these new tasks, we can split the `log` function in two, `local-log` and `remote-log`.
`local-log` is just the old `log` function, writing to standard output, while `remote-log` is the new one which writes over to a socket.
However, if we change the system like this, all of the business functions will have to deal with the details of logging - they will have to know which log to use
For instance, something like this would have to be done to the `is-eligible?` function:
```clojure
(defn is-eligible? [citizen log-type]
  (let [log (if (= log-type :local) local-log remote-log)]
    (log (str "Eligibility check for " (:name citizen)))
    (let [eligible (>= (age citizen) 65)]
      (if (eligible) 
        (log "Citizen is eligible")
        (log "Citizen is not eligible"))
      eligible)))
```

We now have a line to determine which `log` function to use, based on an argument `log-type`. This should work, right?

But oh, crap, this doesn't work yet. When we need to use the `remote-log`, we need to pass `host` and `port` arguments for the connection.
Ok, to solve this, we can re-introduce the `log` based on some kind of `log-output` structure, which would have one of the following formats:
* `{:type :local }`
* `{:type :remote, :host "localhost", :port 3333}`

So now the `log` function can easily query the `:type` field and delegate to the specialist function, such as `remote-log`, passing the remaining arguments individually.

Well, ok, lets see how `is-eligible?` looks like after this change:
```clojure
(defn is-eligible? [citizen log-output]
  (log (str "Eligibility check for " (:name citizen)) log-output)
  (let [eligible (>= (age citizen) 65)]
    (if (eligible) 
      (log "Citizen is eligible" log-output)
      (log "Citizen is not eligible" log-output))
    eligible))
```

Well, this is *better*, but not exactly nice.

-- To be continued... --
