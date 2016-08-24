Well, I don't know how to begin.
Maybe we can talk about objects? Yes, let's do that.

# Objects

So, what are they, really? *Of course, in the context of object-oriented programming, not in general*

The idea of an object in programing had it's birth, at least in history as we know it, when two Algol programmers decided to tweak the language to allow storage of local data and local procedures in the heap instead of the stack.
Lets dive into that a bit more:

Algol was the first language enable the use of blocks, with its `begin` and `end` keywords. Procedures would be declared with the keyword `procedure` followed by a block. This is pretty much what we have today in every other language: switch `procedure` for function and `begin...end` for `{...}` and you get Javascript, for instance.
And very much like Javascript, Algol allowed the programmers to define nested (local) procedures, which could make use of arguments and local variables that were declared in the outer procedure. This is what we call a *closure* in modern terms, the act of wrapping a *block* of code to access variables within the lexical scope of the outer block. In Algol, this lexical scope wlived within the stack-frame of the procedure calls.
For the purpose of illustration, this is how procedure nesting looks like in Algol:
```algol
program A;
var I:integer;
    J:char;

  procedure B;
  var K:real;
      L:integer;
      
    procedure C;
    var M:real;
    begin
      (*scope A+B+C*)
    end;

    (*scope A+B*)
  end; 
  
  (*scope A*)
end.
```
What does any of this have to do with objects? Well, if you look at the structure of the nesting above from a distance, particularly between procedures B and C, you may find it similar to a class definition in any modern language; in this case, we could have a class B with method C and fields K and L as instance variables. 
This is exactly what the Algol guys did, Ole-Johan Dahl and Kristen Nygaard tweaked Algol into their own language, Simula, by turning the mechanisms behing procedure nesting into a way to define what they then called *objects*. 
In essence, they achieved this by switching the storage location of the lexical scope from the stack to the program heap, allowing it to persist in memory after the top procedure returned. This enabled the definition of, say, a "bag of pointers to functions" that were all pointing to the same state in memory, through the arguments passed into the top procedure. In other words, what we now know as objects and constructors.

But, uh, where was I? Ah, objects.
So these guys invented objects, and what are they exactly? Well, by their definition, its simply the encapsulation of data in a lexical scope, shared by procedures, all located in the same region in memory.
