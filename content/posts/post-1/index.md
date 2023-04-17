---
title: "BQN functions and factorization"
date: 2023-04-17
draft: false
---

Disclaimer: I am still a beginner and I may write incorrect things.

In BQN you either define a function as [tacit][tacit-doc] or explicitly within [blocks][blocks-doc]. I mostly code using blocks `{}` because it is easier to program than tacitly.
But once I finish writing my functions, I try to *factorize* them and, even better, to make them tacit.

In BQN you only have a right argument and sometime a left argument, not more. This allow us to factorize the code. For example, if we are in a situation like `{𝕩 𝔽 𝔾 𝕩}` we  use the [before operator][before-doc] `⟜` to remove the duplication of $𝕩$ (and make it tacit!): `𝔽⟜𝔾`. 
 
 
The way I program in BQN is to first write my functions as simple as possible with lot of 𝕩 and 𝕨 and way too much parenthesis. Then, I draw a diagram like the following where I represent the operations order.

![image](images/diagram1.svg)

Finally, I factorize my code by finding patterns in the graph. In this case we get `𝔽⟜𝔾`.

Here is an example a bit more complex, I drew this one when I coded my [planner][planner].

![image](images/diagram2.svg)

This factorization uses a [train][train-doc] which is something I never saw before beginning array programming language.

I am still too inexperienced to know if factorizing everything is a good idea or not. On one hand, it creates a lot of overhead when reading code as the information density is very high but, on the other hand, maybe it is like math where after months of reading the same factorization pattern we just automatically infer the meaning just by looking at it. If it is the latter then it would be very beneficial for me to factor everything I could.

Anyway, here are some patterns and their factorizations. I made this as a cheatsheet for me and other BQN programmers.

![image](images/diagram3.svg)

So now, a little example. Let's say you want to compute the projection of a 2D $p$ point into a line defined by a vector $v$. The formula is $$p' = (p . \frac{v}{||v||})\frac{v}{||v||}$$

And if we draw the operation diagram it would look like this:

![image](images/diagram4.svg)

Let's define the dot product as `DotProd` and the normalization as `Normalize` and we get `(DotProd × ⊢)⟜Normalize`. Of course, in this example it would have been easier to write directly the primitives instead of creating functions.

What I talked today in this post could certainly be automated. I can imagine an editor extension that factorize an expression or develop it to the level the user want. I personally use emacs and my emacs-lisp is not good enough to tackle the challenge but I hope someday somebody will!

[planner]: https://github.com/thibaultbarbie/bqn-planner
[blocks-doc]: https://mlochbaum.github.io/BQN/doc/block.html
[tacit-doc]: https://mlochbaum.github.io/BQN/doc/tacit.html
[before-doc]: https://mlochbaum.github.io/BQN/doc/hook.html
[train-doc]: https://mlochbaum.github.io/BQN/doc/train.html
