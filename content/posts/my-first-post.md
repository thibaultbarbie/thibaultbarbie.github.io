---
title: "My First Post"
date: 2023-04-16T12:19:39+09:00
draft: false
---


I recently tried to learn an array programming language. APL seemed to be the most famous one with J but I did not like the ASCII and the most used APL did not have a license I wanted to work with. I found BQN and it checked all I wanted! However, BQN is a very young language still being developped and while the documentation is comprehensive it is still a bit rough (in my opinion)for beginners. I want to write a simple introduction to BQN that I would have like to have when I began. This is the first post of a serie.

Disclaimer: I am still a beginner and I may write incorrect things.

I will base this post on [a recent program I made][planner]. Here is an exert:

```
DistSqr ← {+´2⋆˜𝕨-𝕩}
```

This code defines a function named `DistSqr` which compute the squared euclidian distance. 
To create a function you need to use `←`. Important, in BQN all user-defined functions have to begin with a capital letter. I often forgot it and BQN will complain about it.

In BQN you either define a function as [tacit][tacit-doc] or explicitly within [blocks][blocks-doc]. In my code I only used blocks `{}` because it is easier to program than tacitly.
A function in BQN can take a left argument 𝕨 and a right argument 𝕩. For example, we could define a (useless) addition function as:

```
Addition ← {𝕨+𝕩}
```

You can use multiple time the arguments inside the function, for example: 

```
Triple ← {𝕩+𝕩+𝕩}
```

However, using multiple time the same arguments may require a lot of parenthesis. For example, let say that a function $$a^5+\sum i$$


[planner]: https://github.com/thibaultbarbie/bqn-planner
[blocks-doc]: https://mlochbaum.github.io/BQN/doc/block.html
[tacit-doc]: https://mlochbaum.github.io/BQN/doc/tacit.html
