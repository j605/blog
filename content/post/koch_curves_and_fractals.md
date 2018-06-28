---
title: "Koch curves and fractals"
date: 2018-06-26T18:03:18+02:00
tags:
  - math
  - plants
  - julia
---
Note: footnotes are broken due to some web issues I don't understand, checkout
https://github.com/gyorb/hugo-dusk/issues/21

Fractals are never ending patterns found in nature that are impossible to
describe in classical geometry. I was intrigued by this topic after a long time
since I had programmed some `koch` curves for an exercise in the Think Python
book.

Some pictures,
![](creations/canvas.jpg)

![](creations/creation2.jpg)
and the code can be found on [github](https://github.com/j605/creations).

The post on how to generate trees[^1]
made me realise that the topic was very general not just related to the curves
I had done earlier. I looked into whether there were already libraries for such
systems and found out that Luxor.jl[^4] provided a great turtle programing[^3]
interface. After installing the library using `Pkg` in `Julia` and running some
example code, I went on to implement the H-tree shown in the post[^1].

The formulation for a H-tree is written as
$$
L(g)→[rf(2^{g/2})L(g+1)][lf(2^{g/2})L(g+1)]
$$

For a more detailed explanation please take a look at the post[^1] and on Lindenmayer
system[^2]. I will briefly explain the terms for the purpose of this post.

The expression can be thought of as a recursive function in programming. So the
first generation, $L(g=0)$, is called the axiom. Actions of turning right and left
are defined as $r$ and $l$. The turtle going forward is represented as $f(n)$.
$[$ and $]$ in the formula refer to pushing and popping from a stack respectively.
So the expression is effectively decoded as

* pushing the current position in the stack
* turning right
* going forward by $L/\sqrt{2}$
* recursing into the next generation which ends in a NOP
* popping from the stack and executing the next branch

A stack is used here for the purpose of branching since state has to saved between
to come back and draw on the other side of the branch. The author of post[^1] also
gives examples of how trees are generated in this way. Furthermore, the system is
parameterized to produced more realistic models of trees.

This gives you a beautiful picture of an H-tree.
![H-tree](creations/htree.svg)

Instead of turning by 90° if you try 60°, a nice fractal is formed.
![H-tree 60°](creations/htree-60.svg)

Julia code for the above drawings is provided below.
```julia
using Luxor, Colors
Drawing(1000, 1000, "htree.svg")
origin()

🐢 = Turtle()

function L(g, l=200, angle=90)
  if g > 200
    return
  end

  Push(🐢)
  Turn(🐢, angle)
  Forward(🐢, l/sqrt(2))
  L(g+20, l/sqrt(2), angle)
  Pop(🐢)

  Push(🐢)
  Turn(🐢, 360-angle)
  Forward(🐢, l/sqrt(2))
  L(g+20, l/sqrt(2), angle)
  Pop(🐢)
end

L(0.0, 200, 60)
finish()
```

[^1]: https://gpfault.net/posts/generating-trees.txt.html
[^2]: https://en.wikipedia.org/wiki/L-system
[^3]: https://en.wikipedia.org/wiki/Turtle_graphics
[^4]: https://juliagraphics.github.io/Luxor.jl/stable/

<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}
});
</script>
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
