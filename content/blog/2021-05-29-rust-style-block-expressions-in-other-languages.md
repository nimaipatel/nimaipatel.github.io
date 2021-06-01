<!-- title: Rust Style Expressions in Other Programming Languages -->
<!-- author: Nimai Patel -->

Lets say we have a variable `animal` and we want to initiate the value of a
variable `sound` based on the value of `animal`. In JavaScript, we could do
something like this:

```
let sound;
if (animal === 'dog') {
    sound = 'barks';
} else if (animal === 'cat') {
    sound = 'meows';
}
```

While this solution works as expected, if you're obsessed with functional
programming like I am, the use of `let` in a codebase that otherwise adheres to
FP concepts as much as possible might bother you.

The immutable solution in this case is actually pretty simple though. We can
just use a ternary operator in the assignment for `sound`

```
const sound = animal === 'dog' ? 'barks' : 'meows'
```

Well, this kinda works. The problem is that `sound` is assigned `'meows'` as long
`animal` isn't `'dog'`. So even if `animal` was `'pig'`, `sound` would be assigned
`'meows'`! Also, what do we do if we have more than two types of animals? We
could start nesting ternary operators, but that would get ugly quickly!

Now the Rust programming language has a syntactically clean solution to this
problem.

```
/* variables are immutable by default in Rust */
let sound = {
    if animal == "dog" {
        "barks"
    } else if animal == "cat" {
        "meows"
    } else if animal == "pig" {
        "oinks"
    } else {
        "not valid animal"
    }
}
```

Expressions inside braces in Rust can evaluate to values. This means you can
use control flow key words like if-else statements and loops during the
assignment of variables.

Now how do we go about doing something similar in other programming languages
like JavaScript? Well, we can use anonymous functions and immediately call
them on assignment.

```
const sound = (() => {
    if (animal === "dog") {
        return "barks"
    } else if (animal === "cat") {
        return "meows"
    } else if (animal === "pig") {
        return "oinks"
    }
})()
```

Great! And it doesn't look that bad either, no?
