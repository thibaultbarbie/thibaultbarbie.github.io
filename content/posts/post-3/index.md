---
title: "Optimizing in BQN"
date: 2023-04-27
draft: false
---
In the [previous post][post2] I describe how I made a collision checking function between multiple line segments and circles. Here is the code:

```
Dot ← +´∘×

# 𝕩≢n‿2‿2    𝕨≢⟨k‿2, k⟩
ColSegmentsCircles ← { 
 [a,b] ← 1⍉𝕩 ⋄ c‿r ← 𝕨 ⋄ r_squared ← r⋆2 
 ca  ← {𝕩 -⎉1 c}⎉1 a ⋄ cb  ← {𝕩 -⎉1 c}⎉1 b
 
 col ← {∨´⎉1}⍟2 r_squared≥⎉1 Dot˜⎉1 {𝕩 -⎉1 c}⎉1 𝕩
 n   ← (-⌾⊑)∘⌽˘ b-a
 d_squared ← (2⋆˜ n {𝕨 Dot⎉1 𝕩}˘ ca ) ÷ Dot˜˘ b-a 
 col ↩ col ∨˘ ∨´˘  (0 ≥ ca Dot˘˘ cb) ∧ {r_squared ≥¨ 𝕩}˘ d_squared
}
```

This code will be used as the base for the path planners and will be the computation bottleneck. As a first draft I am pretty happy with the result but it is not the best I could do. Especially, there are many places in my code where I have to reorder the data axis to perform computations such as this line

```
col ← {∨´⎉1}⍟2 r_squared≥⎉1 Dot˜⎉1 {𝕩 -⎉1 c}⎉1 𝕩
```

I asked for advices on discord and Marshall helped me rewrite the code. His big insight was to change the data representation from `𝕩≢n‿2‿2    𝕨≢⟨k‿2, k⟩` to `𝕩≢2‿n‿2    𝕨≢⟨2‿k, k⟩`. The first axis becomes the $(x,y)$ information. Here is the code with the new representation.

```
# 𝕩≢2‿n‿2    𝕨≢⟨2‿k, k⟩
ColSegmentsCircles2 ← {
 c‿r ← 𝕨 ⋄ cacb ← 𝕩 -⌜˘ c ⋄ ca ← ⊏⎉2 cacb ⋄ ab ← -˝⎉1 𝕩 ⋄ r_sq ← ×˜r  
 n ← (-⌾⊏)∘⌽ ab
 d_sq ← (×˜ +˝ n × ca) ÷ +˝×˜ ab 
 col ← (∨´∘⥊˘ (r_sq⊸≥)⎉1 +˝×˜˘ cacb) ∨ ∨˝˘(0 ≥ +˝ ca × 1⊏⎉2 cacb) ∧ r_sq⊸≥⎉1 d_sq
}
```

We can immediatly see that there are much less use of the rank operator `⎉`. With the new representation the code is able to use constantly the SIMD instructions. 

I benchmarked the two functions and wow! The new code is around 3~10 times faster depending on how much line segments and circles I input. The more we give the better it performs compared to the original function. 

However, this code is still suboptimal. We first test if the segment endpoints are inside the circles and then we test the distance of the circles to the segments. If the first test is positive then we should not perform the second test! Dzaima showed me how to use the under operator `⌾` to only apply the second test on my boolean vector. Here is my final code.

```
# 𝕩≢2‿n‿2    𝕨≢⟨2‿k, k⟩
ColSegmentsCircles3 ← {
 c‿r ← 𝕨 ⋄ r_sq ← ×˜r 
 F ← {∨´∘⥊˘ (r_sq⊸≥)⎉1 +˝×˜ 𝕩 -⌜˘ c}              

 G ← { cacb ← 𝕩 -⌜˘ c ⋄ ca ← ⊏⎉2 cacb ⋄ ab ← -˝⎉1 𝕩 
       n ← (-⌾⊏)∘⌽ ab
       d_sq ← (×˜ +˝ n × ca) ÷ +˝×˜ ab
       ∨˝˘(0 ≥ +˝ ca × 1⊏⎉2 cacb) ∧ r_sq⊸≥⎉1 d_sq}
 m ← ¬F 𝕩 ⋄ col ← (G m /⎉2 𝕩)⌾(m⊸/) (1⊑≢𝕩) ⥊ 1
}
```

This code performs around 2~3 times faster than the previous one and around 4~25 times faster than my original code!

Optimizing in BQN seems to be first and foremost thinking about the data. Instead of randomly ordering the information it is better to think about which operation will be applied to select which shape we should use.

[post2]: https://thibaultbarbie.github.io/posts/post-2/

