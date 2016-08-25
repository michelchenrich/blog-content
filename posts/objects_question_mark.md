# Objects?

Do you know of all the different ways you can retire in your country?
Well, there may not be many different ways where you live, but in Brazil we have plenty.
Imagine we're building a system to allow Brazillian citizens to estimate the different possibilities for retirement, and
that we're building it in a functional programming language, Clojure.

I'll not get into the details of the calculations, it should suffice to say that they are rather complex. For the point I'd like to make, 
the important part is that the complexity of the calculation rules are such that we must also generate a calculation log,
to make sure we understand the resons and the decision tree behind the final result.

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

Now, although we have coupled the `log` function to every business rule function, 
everything should be fine as long as we don't have to deal with the details of logging.
But let's do exactly that - how would this code change if it was necessary to support more than one kind of logging?

For instance, lets suppose the current `log` function simply writes to standard output. 
Now we want to send these messages through a socket, to a specific listener.
In essence, now the logging functionality has the following tasks:
*  Determine which kind of logging has to be done (standard output or socket)
*  Write to standard output when it is the case;
*  Write to the socket when it is the case, which involves:
  *  Connecting to the listener, from an adress and port;
  *  Writing to the socket when the connection was successful;
  *  Writing an error to standard ouput when no connection could be made.

To support these new tasks, we can split the `log` function in two, `local-log` and `remote-log`.
`local-log` is just the old `log` function, writing to standard output, while `remote-log` is the new one which writes over to a socket.
However, if we change the system like this, all of the business functions will have to deal with the details of logging - they will have to know which log to use.
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
Ok, to solve this, we can re-introduce the `log` function and think of a structure to receive the input data for both local and remote logging modes. The structure would have one of the following formats:
*  `{:type :local }`
*  `{:type :remote, :host "localhost", :port 3333}`

So now the `log` function can simply query the `:type` field and delegate to the specialist functions, such as `remote-log`, passing the remaining arguments individually, like this:
```clojure
(defn log [message output]
  (if (= (:type output) :local)
    (local-log message)
    (remote-log (:host output) (:port output) message)))
```

This change now allows the users of `log` to simply pass in the `output` structure and not care anymore about where exactly the log gets written. Now, `is-eligible?` looks like this:
```clojure
(defn is-eligible? [citizen log-output]
  (log (str "Eligibility check for " (:name citizen)) log-output)
  (let [eligible (>= (age citizen) 65)]
    (if (eligible) 
      (log "Citizen is eligible" log-output)
      (log "Citizen is not eligible" log-output))
    eligible))
```

Well, this is better - the business rule functions are no longer coupled to the low-level detail of how logging is done.
But there is still a problem. We want the application to work the same way with the two kinds of logs, but not within the same build.

In other words, we want to be able to build one application using standard output logging, and another with the socket-based logging. With the current solution we have, the code for deciding which kind of log to use would be executed for every log entry, while such a decision should be made in some kind of "assembler" code.

Well, the above description may make it sound farfetched, but this simply means we want the logging behavior to be a plug-in to our application.

-- To be continued... --
