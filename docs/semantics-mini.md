---
title: "Semantic Annotation Mini-Language"
layout: cgreport
---

*Authors*: Bruce Miller, Deyan Ginev (cribbing ideas from Neil Soiffer, Sam Dooley, David Carlisle)

<!-- %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% -->
## Abstract
[MathML 3](https://www.w3.org/TR/MathML3/) is a W3C recommendation for representing
mathematical expressions. It defines two forms:
Presentation MathML describes how the math looks and is the most commonly used;
Content MathML aims to completely describe the math's meaning,
and is consequently difficult to produce.
The purpose of this note is to develop a means of adding
at least minimal semantic information to Presentation MathML to support accessibility.

<nav id="toc" markdown="1">

# Table of Contents
{:.no_toc}

* toc
{:toc}

</nav>

<!-- %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% -->
## Motivation
Mathematical notations are ambiguous.
Some notations are used for different purposes and perhaps should be spoken differently.
In contrast, different communities occasionally express the same mathematical concept
using different notations; perhaps they should be spoken the same.
Consider the variety of purposes for which a superscript is employed.
Most commonly, one writes $x^2$ or $x^n$ for powers;
abstractly, power is applied to 2 arguments.
But superscripts are also used for indexing, such as with tensors or matrices $\Phi^i$.
Abstractly,  a different operation is applied the same 2 arguments;
one needs to know the subject area or topic to know which operator is intended.

It gets worse: sometimes the superscript itself indicates the intended operator:
$z^*$, $A^T$, $A^\dagger$, may signify conjugate, transpose and adjoint
operations applied to the base of the subscript.
Of course, these operations could also be written in functional notation,
$\mathrm{conj}(z)$, $\mathrm{trans}(A)$, $\mathrm{adj}(A)$,
so we ought not conflate the meaning "transpose of A"
with the specific superscript notation that was used.

But sometimes a cigar is just a cigar. While in some contexts, $x'$ might represent
the derivative of $x$, in other contexts the $x'$ as a whole simply represents "a different x",
or perhaps "the frobulator".

Another notational pattern is seen in the somewhat messy markup
commonly used for a binomial coefficient $\binom{n}{m}$,
having the arguments $n$ and $m$ are buried in the markup, stacked with either `mfrac` or `mtable`,
and finally wrapped in parentheses.
The fact that very similar markup is also used for Eulerian numbers, Legendre and Jacobi symbols, 
and conversely, that other notations, such as $C^n_m$, are used for binomial coefficients,
suggest decoupling the notation and meaning of the expression.

The common theme is the disambiguation of seemingly identical notations
as well as variety of patterns that must be recognized by a accessibility agent
which may want to use some combination of the mathematical intent of an expression
as well as the actual notation employed in order to generate effective speech.
Here we focus on a minimal annotation of Presentation MathML that could resolve
the inherent ambiguities and facilitate more useful accessibility.
The focus is not necessarily to achieve the precision necessary for computation.

<!-- %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% -->
## Proposal
We propose an attribute, tentatively called "intent",
which describes the semantic intent of a Presentation MathML fragment
(the node on which the attribute is given)
using a simple prefix notation. This mechanism provides for recursively
describing the operator and arguments, as well as ascribing a fixed intent
to entire subtrees.
The mini-language is specified as follows:

```
intent   ::= literal | selector | intent '(' intent [ ',' intent ]* ')'
literal  ::= [letters|digits|_|-]+
selector ::= argref | argpath
argref   ::= '$' NCName
argpath  ::= '$' [digit]+ [ '/' [digit]+ ]*
```

where the grammar components have the following significance:
* the third form of intent represents some operation applied to arguments.
* literal: represents a mathematical concept, being
a literal keyword (to be looked up in a to-be-specified dictionary),
or a phrase to be spoken as-is (possibly translated, as needed) if not present in the dictionary.
The set of literals is intentionally open-ended.
* selector: selects another node whose intent is used in-place-of or as part-of the composed intent.
* argref: refers to the intent of another node,
being the child of the current node with attribute `arg` having the given `NCName`
(or possibly any node in the document with the `NCName` as `id`).
* argpath: refers to the intent of a descendant of the current node;
 for example, a single component such as `$2` refers to the 2nd child;
 multiple components refer to grandchildren or deeper,
 `$1/1` would refer to the first grandchild.

The key component here is the literal;
they are intended to correspond to some mathematical concept
or operator, or some application specific quantity or operation;
that is it represents some "meaning".
Ultimately, the goal is to *translate* this virtual content tree into
text or speech (in different languages), braille or some other form.
The preferred translation may also depend on context and user profile.
On the one hand, the most natural translation of a given expression
can depend on the operator.  It is thus desirable to have a set of
common known meaning with translation rules.
Thus `power(x,2)` might be rendered as "the 2nd power of x" or "x squared",
depending on the user and agent.

On the other hand, mathematics encompasses an endless set of concepts,
arguing that the set of meanings must be open-ended.
We propose that there should be a small set of recommended meaning keywords
whose translation can be specialized, while allowing any value for the meaning
(an implementation is free to recognize more keywords).
In the case where a meaning is *not* in a dictionary,
support for multiple languages may be weak and
the application of such a literal would be generic
as "the [meaning] of [arg1], [arg2] and [argn] ...".
While this result may be less than optimal, it is still functional.

<!-- %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% -->
## Some Examples

### Same notation, different meanings

With this model, the `msup` examples:
$x^n$ as a power;
$A^T$ as a transpose;
$f'$ as a derivative;
and $x'$ as an embellished symbol
would be distinguished as follows:
```
<msup intent="power($base,$exp)">
  <mi arg="base">x</mi>
  <mi arg="exp">n</mi>
</msup>
```

```
<msup intent="$op($a)">
  <mi arg="a">A</mi>
  <mi arg="op" intent="transpose">T</mi>
</msup>
```
(easily adapted to conjugate and adjoint)

```
<msup intent="derivative($a)">
  <mi arg="a">f</mi>
  <mi>'</mi>
</msup>
```

```
<msup intent="x-prime">
  <mi>x</mi>
  <mo>'</mo>
</msup>
```

### Selectors

Use of selectors allow different ways to express the same result, such as:
```
<msup intent="transpose(A)">
  <mi>A</mi>
  <mi>T</mi>
</msup>
```
```
<msup intent="transpose($a)">
  <mi arg="a">A</mi>
  <mi>T</mi>
</msup>
```
```
<msup intent="$mop($marg)">
  <mi arg="marg">A</mi>
  <mi arg="mop" intent="transpose">T</mi>
</msup>
```
```
<msup intent="transpose($1)">
  <mi>A</mi>
  <mi>T</mi>
</msup>
```
At one extreme, one could simply encode the entire intent on the `math` element.
However, liberal use of selectors presumably would give the user agent
more leeway for highlighting, navigation, etc.

### Different notations, same meaning

A binomial would be marked up as:
```
<mrow intent="binomial($n,$m)">
  <mo>(</mo>
  <mfrac thickness="0pt">
    <mi arg="n">n</mi>
    <mi arg="m">m</mi>
  </mfrac>
  <mo>)</mo>
</mrow>
```
However, other notations for binomial are in use, such as:

```
<msubsup intent="$op($n,$m)">
  <mi arg="op" intent="binomial">C</mi>
  <mi arg="n">n</mi>
  <mi arg="m">m</mi>
</msubsup>
```
Or as some would prefer
```
<msubsup intent="$op($n,$m)">
  <mi arg="op" intent="binomial">C</mi>
  <mi arg="m">m</mi>
  <mi arg="n">n</mi>
</msubsup>
```
Each of the above binomials have the same semantic content,
and (presumably) would generate the same translation.

### mrow structure

A row of infix with multiple operators may seem to be a special case,
depending on how `mrow` is used, but basically it forces the annotator
to specify the exact nesting and precedence of operators:
```
<mrow intent="$p($a,$b,$m($c),$d)">
  <mi arg="a">a</mi>
  <mo arg="p" intent="plus">+</mo>
  <mi arg="b">b</mi>
  <mo arg="m" intent="minus">-</mo>
  <mi arg="c">c</mi>
  <mo>+</mo>
  <mi arg="d">d</mi>
</mrow>
```
Such expressions can be annotated whether the markup
is rich or poor in `mrow`s; one only has to annotate at the appropriate
level in the tree.

A variety of infix operators ($\times$, $\cdot$, etc) are handled by
adding the appropriate meaning to each.

Prefix and Postfix operators, are simple, provided they are contained
within an ```mrow```:
```
<mrow intent="$op($a)">
  <mi arg="a">n</mi>
  <mo arg="op" intent="factorial">!</mo>
</mrow>
```
A little less simple, but still possible, if they are not
```
<mrow intent="$p($a,$f($b))">
  <mi arg="a">a</mi>
  <mo arg="p" intent="plus">+</mo>
  <mi arg="b">b</mi>
  <mo arg="f" intent="factorial">!</mo>
</mrow>
```

<!-- %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% -->
## Defaulting
"Defaulting" is based on the idea that certain notations
are almost always used for the same purpose in a given subject area,
and that therefore an author ought not need to tediously annotate every single instance;
the system should recognize the usage pattern an generate the intent annotation itself.
This document does not directly address the subject of defaulting,
although the intent model presented here may very well serve as the
target language for a defaulting preprocessor (or processing phase).

However, the possibility of a defaulting process does have implications
for how one should annotate.  A node without an explicitly given intent would likely
be a candidate for a default intent based on some rule set.
In a field where a prime is commonly used to denote derivatives,
the annotator must make sure to mark those primes that do *not* denote
derivatives, such as the $x'$ example above.

<!-- %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% -->
## Summary
We have proposed a simple intent attribute to annotate Presentation MathML
with disambiguating information that can be used to support accessibility.
The design is open-ended.  Further topics that will need to be addressed are:
* dictionary: being a catalog of common semantic concepts with special requirements
for quality speech;
* defaulting: a mechanism to automatically generate intent annotation based on subject area.


<!-- %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% -->
## Appendix: Examples of notations
Note that often the same meaning will appear within different notations.

<!-- ======================================================================
  Big problems laying out tables. The goal is to embed multi-line quoted code
  within a table cell (ie. must be a parsed block).  I can't achieve this with "pipe" code.
  So I use raw HTML. A blank-line should end raw HTML, but does NOT in Jekyll.
  OTOH, there's a brace-colon-colon directive nomarkdown in kramdown;
  that works in Jekyll (& the github.io site), but not on github's standard .md
  -->

{::nomarkdown}
<table>
<thead><tr><th>Notation</th><th>Description</th><th>Code</th></tr></thead>
<tbody>
<!-- ======================================== -->
<tr><td> infix </td><td> arithmetic<br/> $a+b-c+d$ </td><td>
{:/nomarkdown}
```
<mrow intent="$op1($arg1,$arg2,$op2($arg3),$arg4)">
  <mi arg="arg1">a</mi>
  <mo arg="op1" intent="plus">+</mo>
  <mi arg="arg2">b</mi>
  <mo arg="op2" intent="minus">-</mo>
  <mi arg="arg3">c</mi>
  <mo>+</mo>
  <mi arg="arg4">d</mi>
</mrow>
```
{::nomarkdown}
</td></tr>

<tr><td>  </td><td> inner product $\mathbf{a}\cdot\mathbf{b}$ </td><td>
{:/nomarkdown}
```
<mrow intent="$op($arg1,$arg2)">
  <mi arg="arg1" mathvariant="bold">a</mi>
  <mo arg="op" intent="inner-product>&#x22C5;</mo>
  <mi arg="arg2" mathvariant="bold">b</mi>
</mrow>
```
{::nomarkdown}
</td></tr>
<tr><td/><td colspan="2">Easily extended to other operators and meanings: cross-product, "by", etc.</td></tr>
<!-- ======================================== -->
<tr><td> prefix </td><td> negation $-a$ </td><td>
{:/nomarkdown}
```
<mrow intent="$op($arg)">
  <mo arg="op" intent="unary-minus">-</mo>
  <mi arg="arg">a</mi>`
</mrow>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> Laplacian $\nabla^2 f$ </td><td>
{:/nomarkdown}
```
<mrow intent="$op($arg)">
  <msup arg="op" intent="laplacian">
    <mi>&#x2207;</mi>
    <mn>2</mn>
  </msup>
  <mi arg="arg">f</mi>`
</mrow>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
<tr><td> postfix </td><td> factorial $n!$ </td><td>
{:/nomarkdown}
```
<mrow intent="$op($arg)">
  <mi arg="arg">a</mi>
  <mo arg="op" intent="factorial">!</mo>
</mrow>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
<tr><td> sup </td><td> power $x^n$ </td><td>
{:/nomarkdown}
```
<msup intent="power($base,$exp)">
  <mi arg="base">x</mi>
  <mi arg="exp">n</mi>
</msup>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> iterated function <br/> $f^n$ ($=f(f(...f))$)</td><td>
{:/nomarkdown}
```
<msup intent="functional-power($op,$n)">
  <mi arg="op">f</mi>
  <mi arg="n">n</mi>
</msup>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> inverse $\sin^{-1}$ </td><td>
{:/nomarkdown}
```
<msup intent="functional-power($op,$n)">
  <mi arg="op">sin</mi>
  <mn arg="n">-1</mn>
</msup>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> $n$-th derivative $f^{(n)}$ </td><td>
{:/nomarkdown}
```
<msup intent="derivative-implicit-variable($op,$n)">
  <mi arg="op">f</mi>
  <mrow>
    <mo>(</mo>
    <mi arg="n">n</mi>
    <mo>)</mo>
  </mrow>
</msup>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
<tr><td> sub </td><td> indexing $a_i$ </td><td>
{:/nomarkdown}
```
<msup intent="index($array,$index)">
  <mi arg="array">a</mi>
  <mi arg="index">i</mi>
</msup>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
<tr><td> sup-operator </td><td> transpose $A^T$ </td><td>
{:/nomarkdown}
```
<msup intent="$op($x)">
  <mi arg="x">A</mi>
  <mi arg="op" intent="transpose">T</mn>
</msup>
```
{::nomarkdown}
</td></tr>
<tr><td> </td><td> Compare to $\mathrm{trans}(A)$ </td>
<td>
{:/nomarkdown}
```
<mrow intent="$op($x)">
  <mi arg="op" intent="transpose">trans</mi>
  <!-- optionally &ApplyFunction; -->
  <mi arg="x">A</mn>
</mrow>
```
{::nomarkdown}
</td>
</tr>
<tr><td> </td><td> Or the function $\mathrm{trans}$ </td>
<td>
{:/nomarkdown}
```
<mi intent="transpose">trans</mi>
```
{::nomarkdown}
</td>
</tr>
<tr><td> </td><td> adjoint $A^\dagger$ </td><td>
{:/nomarkdown}
```
<msup intent="$op($x)">
  <mi arg="x">A</mi>
  <mi arg="op" intent="adjoint">&dagger;</mn>
</msup>
```
{::nomarkdown}
</td></tr>
<tr><td> </td><td> $2$-nd derivative $f''$ </td><td>
{:/nomarkdown}
```
<msup intent="derivative-implicit-variable($op,$n)">
  <mi arg="op">f</mi>
  <mo arg="n" intent="2">''</mo>
</msup>
```
{::nomarkdown}
</td></tr>
<tr><td>Awkward nesting</td><td> $x'_i$ </td><td>
{:/nomarkdown}
```
 <msubsup intent="derivative-implicit-variable(index($array,$index))">
   <mi arg="array">x</mi>
   <mi arg="index">i</mi>
   <mo>'</mo>
  </msubsup>
```
{::nomarkdown}
</td></tr>
<tr><td></td><td> or maybe</td><td>
{:/nomarkdown}
```
 <msubsup intent="index(derivative-implicit-variable($op),$index)">
   <mi arg="op">x</mi>
   <mi arg="index">i</mi>
   <mo>'</mo>
  </msubsup>
```
{::nomarkdown}
</td></tr>
<tr><td></td><td>  $\overline{x}_i$ being midpoint of $x_i$</td>
<td>
{:/nomarkdown}
```
 <msub intent="$op(index($line,$index))">
    <mover accent="true">
      <mi arg="line">x</mi>
      <mo arg="op" intent="midpoint">¯</mo>
    </mover>
    <mi arg="index">i</mi>
  </msub>
```
{::nomarkdown}
</td>
</tr>
<tr><td></td><td> Versus: $\overline{x}_i$ being ith element of $\overline{x}$ </td>
<td>
{:/nomarkdown}
```
 <msub intent="index($arr,$index)">
    <mover arg="arr" accent="true" intent="$op($line)>
      <mi arg="line">x</mi>
      <mo arg="op" intent="midpoint">¯</mo>
    </mover>
    <mi arg="index">i</mi>
  </msub>
```
{::nomarkdown}
</td>
</tr>

<!-- ======================================== -->
<tr><td> base-operator </td><td> binomail $C^n_m$ </td><td>
{:/nomarkdown}
```
<msubsup intent="$op($n,$m)">
  <mi arg="op" intent="binomial">C</mi>
  <mi arg="m">m</mi>
  <mi arg="n">n</mi>
</msubsup>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
<!--
<tr><td> fenced </td><td> grouping $(a+b)$ </td><td>
{:/nomarkdown}
```
<mrow>
  <mo>(</mo>
  <mi>a</mi>
  <mo>+</mo>
  <mi>b</mi>
  <mo>)</mo>
</msup>
```
{::nomarkdown}
</td></tr>
-->
<tr><td> fenced </td><td> absolute value $|x|$ </td><td>
{:/nomarkdown}
```
<mrow intent="absolute-value($x)">
  <mo>|</mo>
  <mi arg="x">x</mi>
  <mo>|</mo>
</msup>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> norm $|\mathbf{x}|$ </td><td>
{:/nomarkdown}
```
<mrow intent="norm($x)">
  <mo>|</mo>
  <mi arg="x"> mathvariant="bold"x</mi>
  <mo>|</mo>
</msup>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> determinant $|\mathbf{X}|$ </td><td>
{:/nomarkdown}
```
<mrow intent="determinant($x)">
  <mo>|</mo>
  <mi arg="x" mathvariant="bold">X</mi>
  <mo>|</mo>
</msup>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> sequence $\lbrace a_n\rbrace$ </td><td>
{:/nomarkdown}
```
<mrow intent="sequence($arg)">
  <mo>{</mo>
  <msub arg="arg">
    <mi>x</mi>
    <mi>n</mi>
  </msub>
  <mo>}</mo>
</msup>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> open interval $(a,b)$ </td><td>
{:/nomarkdown}
```
<mrow intent="open-interval($a,$b)">
  <mo>(</mo>
  <mi arg="a">a</mi>
  <mo>,</mo>
  <mi arg="b">b</mi>
  <mo>)</mo>
</msup>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> open interval $]a,b[$ </td><td>
{:/nomarkdown}
```
<mrow intent="open-interval($a,$b)">
  <mo>]</mo>
  <mi arg="a">a</mi>
  <mo>,</mo>
  <mi arg="b">b</mi>
  <mo>[</mo>
</msup>
```
{::nomarkdown}
</td></tr>
<tr><td/><td colspan="2">closed, open-closed, etc. similarly</td></tr>

<tr><td> </td><td> inner product $\left<\mathbf{a},\mathbf{b}\right>$</td><td>
{:/nomarkdown}
```
<mrow intent="inner-product($a,$b)">
  <mo>&lt;</mo>
  <mi arg="a" mathvariant="bold">a</mi>
  <mo>,</mo>
  <mi arg="b" mathvariant="bold">b</mi>
  <mo>&gt;</mo>
</msup>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> Legendre symbol $(n|p)$</td><td>
{:/nomarkdown}
```
<mrow intent="Legendre-symbol($n,$p)">
  <mo>(</mo>
  <mi arg="n">n</mi>
  <mo>|</mo>
  <mi arg="p">p</mi>
  <mo>)</mo>
</msup>
```
{::nomarkdown}
</td></tr>
<tr><td/><td>Jacobi symbol</td><td>similarly</td></tr>

<tr><td> </td><td> Clebsch-Gordan<br/> $(j_1 m_1 j_2 m_2 | j_1 j_2 j_3 m_3)|$</td><td>
{:/nomarkdown}
```
<mrow intent="Clebsch-Gordan($a1,$a2,$a3,$a4,$b1,$b2,$b3,$b4)">
  <mo>(</mo>
  <msub arg="a1"><mi>j</mi><mn>1</mn>
  <msub arg="a2"><mi>m</mi><mn>1</mn>
  <msub arg="a3"><mi>j</mi><mn>2</mn>
  <msub arg="a4"><mi>m</mi><mn>2</mn>
  <mo>|</mo>
  <msub arg="b1"><mi>j</mi><mn>1</mn>
  <msub arg="b2"><mi>j</mi><mn>2</mn>
  <msub arg="b3"><mi>j</mi><mn>3</mn>
  <msub arg="b4"><mi>m</mi><mn>3</mn>
  <mo>)</mo>
</msup>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
<tr><td>fenced-sub </td><td> Pochhammer $\left(a\right)_n$ </td><td>
{:/nomarkdown}
```
<msup intent="Pochhammer($a,$n)">
  <mrow>
    <mo>(</mo>
    <mi arg="a">a</mi>
    <mo>)</mo>
  </mrow>
  <mi arg="n">n</mi>
</msup>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
<tr><td>fenced-stacked </td><td> binomial $\binom{n}{m}$ </td><td>
{:/nomarkdown}
<!-- <mrow intent="binomial($2/1,$2/2)"> -->
```
<mrow intent="binomial($n,$m)">
  <mo>(</mo>
  <mfrac thickness="0pt">
    <mi arg="n">n</mi>
    <mi arg="m">m</mi>
  </mfrac>
  <mo>)</mo>
</mrow>
```
{::nomarkdown}
</td></tr>

<tr><td> </td><td> multinomial $\binom{n}{m_1,m_2,m_3}$ </td><td>
{:/nomarkdown}
<!-- <mrow intent="multinomial($2/1,$2/2/1,$2/2/3,$2/2/5)"> -->
```
<mrow intent="multinomial($n,$m1,$m2,$m3)">
  <mo>(</mo>
  <mfrac thickness="0pt">
    <mi arg="n">n</mi>
    <mrow>
      <msub arg="m1"><mi>m</mi><mn>1</mn></msup>
      <mo>,</mo>
      <msub arg="m2"><mi>m</mi><mn>2</mn></msup>
      <mo>,</mo>
      <msub arg="m3"><mi>m</mi><mn>3</mn></msup>
    </mrow>
  </mfrac>
  <mo>)</mo>
</mrow>
```
{::nomarkdown}
</td></tr>
<tr><td/><td/><td>??? puntuation separates the several arguments?</td></tr>

<tr><td> </td><td> Eulerian numbers $\left< n \atop k \right>$ </td><td>
{:/nomarkdown}
```
<mrow intent="Eulerian-numbers($n,$k)">
  <mo>&lt;</mo>
  <mfrac thickness="0pt">
    <mi arg="n">n</mi>
    <mi arg="k">k</mi>
  </mfrac>
  <mo>&gt;</mo>
</mrow>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
<tr><td>fenced-table</td><td> 3j symbol<br/> $\left(\begin{array}{ccc}j_1& j_2 &j_3 \\ m_1 &m_2 &m_3\end{array}\right)$</td><td>
{:/nomarkdown}
<!-- <mrow intent="3j($2/1/1,$2/1/2,$2/1/3,$2/2/1,$2/2/2,$2/2/3)">-->
```
<mrow intent="3j($j1,$j2,$j3,$m1,$m2,$m3)">
  <mo>(</mo>
  <mtable>
    <mtr>
      <mtd arg="j1"><msub><mi>j</mi><mn>1</mn></mtd>
      <mtd arg="j2"><msub><mi>j</mi><mn>2</mn></mtd>
      <mtd arg="j3"><msub><mi>j</mi><mn>3</mn></mtd>
    </mtr>
    <mtr>
      <mtd arg="m1"><msub><mi>m</mi><mn>1</mn></mtd>
      <mtd arg="m2"><msub><mi>m</mi><mn>2</mn></mtd>
      <mtd arg="m3"><msub><mi>m</mi><mn>3</mn></mtd>
    </mtr>
  </mtable>
  <mo>)</mo>
</msup>
```
{::nomarkdown}
</td></tr>
<tr><td/><td>6j, 9j, ...</td><td>similarly</td></tr>
<!-- ======================================== -->
<tr><td>functions</td><td> function $A(a,b;z|q)$</td><td>
{:/nomarkdown}
```
<mrow intent="$op($p1,$p2,$a1,$q)">
  <mi arg="op">A</mi>
  <mo>(</mo>
  <mi arg="p1">a</mi>
  <mo>,</mo>
  <mi arg="p2">b</mi>
  <mo>;</mo>
  <mi arg="a1">z</mi>
  <mo>|</mo>
  <mi arg="q">q</mi>
  <mo>(</mo>
</mrow>
```
{::nomarkdown}
</td></tr>
<tr><td></td><td> Bessel $J_\nu(z)$</td><td>
{:/nomarkdown}
```
<mrow intent="$op($nu,$z)">
  <msub>
    <mi arg="op" intent="BesselJ">J</mi>
    <mi arg="nu">&#x3BD;</mi>
  </msub>
  <mo>(</mo>
  <mi arg="z">z</mi>
  <mo>(</mo>
</mrow>
```
{::nomarkdown}
</td></tr>
<tr><td></td><td> curried Bessel $J_\nu(z)$</td><td>
{:/nomarkdown}
```
<mrow intent="$op($nu)($z)">
  <msub>
    <mi arg="op" intent="BesselJ">J</mi>
    <mi arg="nu" >&#x3BD;</mi>
  </msub>
  <mo>(</mo>
  <mi arg="z">z</mi>
  <mo>(</mo>
</mrow>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
<tr><td>derivatives</td><td> $\frac{d^2f}{dx^2}$</td><td>
{:/nomarkdown}
<!-- <mfrac intent="Leibnitz-derivative($1/2,$2/1/2,$1/1/2)"> -->
```
<mfrac intent="Leibnitz-derivative($func,$var,$deg)">
  <mrow>
    <msup>
      <mo>d</mo>
      <mn>2</mn>
    </msup>
    <mi arg="func">f</mix>
  </mrow>
  <msup>
    <mrow>
      <mo>d</mo>
      <mi arg="var">x</mix>
    </mrow>
    <mn arg="deg">2</mn>
  </msup>
</mfrac>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
<tr><td>integrals</td><td> $\int\frac{dr}{r}$</td><td>
{:/nomarkdown}
```
<mrow intent="$op(divide(1,$r),$bvar)">
  <mo arg="op" intent="integral">&#x222B;</mo>
  <mfrac>
    <mrow>
      <mi>d</mi>
      <mi arg="bvar">r</mi>
    </mrow>
    <mi arg="r">r</mi>
  </mfrac>
</mrow>
```
{::nomarkdown}
</td></tr>
<tr><td/><td colspan="2">One might be tempted put intent="divide(1,$r)" on the mfrac, but this blocks access to $bvar</td></tr>
<!-- ======================================== -->
<tr><td>continued fractions</td><td> $a_0+\displaystyle\frac{1}{a_1+\displaystyle\frac{1}{a_2+\cdots}}$</td><td>
{:/nomarkdown}
<!--<mrow intent="infinite-continued-fraction($1,1,$3/1/2/1,1,$3/1/2/3/1/2)">-->
```
<mrow intent="infinite-continued-fraction($a0,$b1,$a1,$b2,$a2)">
  <msub arg="a0"><mi>a</mi><mn>0</mn></msub>
  <mo>+</mo>
  <mstyle display="true">
    <mfrac>
      <mn arg="b1">1</mn>
      <mrow>
        <msub arg="a1"><mi>a</mi><mn>1</mn></msub>
        <mo>+</mo>
        <mstyle display="true">
          <mfrac>
            <mn arg="b2">1</mn>
            <mrow>
              <msub arg="a2"><mi>a</mi><mn>2</mn></msub>
              <mo>+</mo>
              <mo>&#x22EF;</mo>
            </mrow>
          </mfrac>
        </mstyle>
      </mrow>
    </mfrac>
  </mstyle>
</mrow>
```
{::nomarkdown}
</td></tr>
<!-- ======================================== -->
</tbody>
</table>
{:/nomarkdown}

<!-- %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% -->
