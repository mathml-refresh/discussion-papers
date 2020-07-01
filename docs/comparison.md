---
title: "Semantic Annotation Mini-Language"
---

<style>
.container-lg {max-width:100%;  font-size:90%;}
</style>

*Authors*:  Neil Soiffer, and unkowningly Bruce Miller, Deyan Ginev,

<!-- %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% -->
## Side-by-Side Comparison Between Proposals
This extracts out the table from Bruce's and Deyan's proposal and adds in my proposal.
The proposals have many similarities.
Putting them side by side hopefully makes it easier to compare the differences.
 

{::nomarkdown}
<table>
<thead><tr><th>Notation</th><th>Description</th><th>Brue & Deyan proposal</th><th>Neil arg proposal</th><th>Neil position proposal</th></tr></thead>
<tbody>
<!-- ======================================== -->
<tr><td> infix </td><td> arithmetic<br/> $a+b-c+d$ </td><td>
{:/nomarkdown}
```
<mrow semantic="#op1(#arg1,#arg2,#op2(#arg3),#arg4)">
  <mi arg="arg1">a</mi>
  <mo arg="op1" semantic="plus">+</mo>
  <mi arg="arg2">b</mi>
  <mo arg="op2" semantic="minus">-</mo>
  <mi arg="arg3">c</mi>
  <mo>+</mo>
  <mi arg="arg4">d</mi>
</mrow>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<mrow semantic="plus-minus(@arg1,@arg2,@op2,@arg3,@arg4,@arg5)">
  <mi arg="arg1">a</mi>
  <mo arg="op1">+</mo>
  <mi arg="arg2">b</mi>
  <mo arg="op2">-</mo>
  <mi arg="arg3">c</mi>
  <mo arg="arg4">+</mo>
  <mi arg="arg5">d</mi>
</mrow>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<mrow semantic="plus-minus(@*)">
  <mi>a</mi>
  <mo>+</mo>
  <mi>b</mi>
  <mo>-</mo>
  <mi>c</mi>
  <mo>+</mo>
  <mi>d</mi>
</mrow>
```
{::nomarkdown}
</td></tr>

<tr><td>  </td><td> inner product $\mathbf{a}\cdot\mathbf{b}$ </td>
<td>
{:/nomarkdown}
```
<mrow semantic="#op(#arg1,#arg2)">
  <mi arg="arg1" mathvariant="bold">a</mi>
  <mo arg="op" semantic="inner-product>&#x22C5;</mo>
  <mi arg="arg2" mathvariant="bold">b</mi>
</mrow>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<mrow semantic="inner-product(@arg1,@op,@arg2)">
  <mi arg="arg1" mathvariant="bold">a</mi>
  <mo arg="op">&#x22C5;</mo>
  <mi arg="arg2" mathvariant="bold">b</mi>
</mrow>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<mrow semantic="inner-product(@*)">
  <mi mathvariant="bold">a</mi>
  <mo>&#x22C5;</mo>
  <mi mathvariant="bold">b</mi>
</mrow>
```
{::nomarkdown}
</td>
</tr>
<tr><td/><td colspan="2">Easily extended to other operators and meanings: cross-product, "by", etc.</td></tr>
<!-- ======================================== -->
<tr><td> prefix </td><td> negation $-a$ </td>
<td>
{:/nomarkdown}
```
<mrow semantic="#op(#arg)">
  <mo arg="op" semantic="unary-minus">-</mo>
  <mi arg="arg">a</mi>`
</mrow>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<mrow semantic="unary-minus(@op, @arg)">
  <mo arg="op">-</mo>
  <mi arg="arg">a</mi>`
</mrow>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<mrow semantic="unary-minus(@*)">
  <mo>-</mo>
  <mi>a</mi>`
</mrow>
```
{::nomarkdown}
</td>
</tr>

<tr><td> </td><td> Laplacian $\nabla^2 f$ </td>
<td>
{:/nomarkdown}
```
<mrow semantic="#op(#arg)">
  <msup arg="op" semantic="laplacian">
    <mi>&#x2207;</mi>
    <mn>2</mn>
  </msup>
  <mi arg="arg">f</mi>`
</mrow>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<mrow semantic="laplacian(@op, @arg)">
  <msup arg="op">
    <mi>&#x2207;</mi>
    <mn>2</mn>
  </msup>
  <mi arg="arg">f</mi>`
</mrow>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<mrow semantic="laplacian(@*)">
  <msup>
    <mi>&#x2207;</mi>
    <mn>2</mn>
  </msup>
  <mi>f</mi>`
</mrow>
```
{::nomarkdown}
</td>
</tr>
<!-- ======================================== -->
<tr><td> postfix </td><td> factorial $n!$ </td>
<td>
{:/nomarkdown}
```
<mrow semantic="#op(#arg)">
  <mi arg="arg">a</mi>
  <mo arg="op" semantic="factorial">!</mo>
</mrow>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<mrow semantic="factorial(@arg, @op)">
  <mi arg="arg">a</mi>
  <mo arg="op" semantic="factorial">!</mo>
</mrow>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<mrow semantic="factorial(@*)">
  <mi>a</mi>
  <mo>!</mo>
</mrow>
```
{::nomarkdown}
</td>
</tr>
<!-- ======================================== -->
<tr><td> sup </td><td> power $x^n$ </td>
<td>
{:/nomarkdown}
```
<msup semantic="power(#base,#exp)">
  <mi arg="base">x</mi>
  <mi arg="exp">n</mi>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="power(@base,@exp)">
  <mi arg="base">x</mi>
  <mi arg="exp">n</mi>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="power(@*)">
  <mi>x</mi>
  <mi>n</mi>
</msup>
```
{::nomarkdown}
</td>
</tr>

<tr><td> </td><td> repeated application <br/> $f^n$ ($=f(f(...f))$)</td>
<td>
{:/nomarkdown}
```
<msup semantic="applicative-power(#op,$n)">
  <mi arg="op">f</mi>
  <mi arg="n">n</mi>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="applicative-power(@op,@n)">
  <mi arg="op">f</mi>
  <mi arg="n">n</mi>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="applicative-power(@*)">
  <mi>f</mi>
  <mi>n</mi>
</msup>
```
{::nomarkdown}
</td>
</tr>

<tr><td> </td><td> inverse $\sin^{-1}$ </td>
<td>
{:/nomarkdown}
```
<msup semantic="applicative-power(#op,#n)">
  <mi arg="op">sin</mi>
  <mn arg="n">-1</mn>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="applicative-power(@op,@n)">
  <mi arg="op">sin</mi>
  <mn arg="n">-1</mn>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="applicative-power(@*)">
  <mi>sin</mi>
  <mn>-1</mn>
</msup>
```
{::nomarkdown}
</td>
</tr>

<tr><td> </td><td> $n$-th derivative $f^{(n)}$ </td>
<td>
{:/nomarkdown}
```
<msup semantic="derivative-implicit-variable(#op,#n)">
  <mi arg="op">f</mi>
  <mrow>
    <mo>(</mo>
    <mi arg="n">n</mi>
    <mo>)</mo>
  </mrow>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="derivative-implicit-variable(@op,@n)">
  <mi arg="op">f</mi>
  <mrow>
    <mo>(</mo>
    <mi arg="n">n</mi>
    <mo>)</mo>
  </mrow>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="derivative-implicit-variable(@1,@2@1)">
  <mi>f</mi>
  <mrow>
    <mo>(</mo>
    <mi>n</mi>
    <mo>)</mo>
  </mrow>
</msup>
```
{::nomarkdown}
</td>
</tr>
<!-- ======================================== -->
<tr><td> sub </td><td> indexing $a_i$ </td>
<td>
{:/nomarkdown}
```
<msup semantic="index(#array,#index)">
  <mi arg="array">a</mi>
  <mi arg="index">i</mi>
</msup>
```
{::nomarkdown}
</td>
<tr><td> sub </td><td> indexing $a_i$ </td>
<td>
{:/nomarkdown}
```
<msup semantic="index(@array,@index)">
  <mi arg="array">a</mi>
  <mi arg="index">i</mi>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="index(@*)">
  <mi>a</mi>
  <mi>i</mi>
</msup>
```
{::nomarkdown}
</td>
</tr>
<!-- ======================================== -->
<tr><td> sup-operator </td><td> transpose $A^T$ </td>
<td>
{:/nomarkdown}
```
<msup semantic="#op(#x)">
  <mi arg="x">A</mi>
  <mi arg="op" semantic="transpose">T</mn>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
For speech, we need two different transpose functions ("A transpose" vs "transpose of A for T(A)") or the speech needs to find the "operator" and deduce the form from that.
```
<msup semantic="transpose(@x, @op)">
  <mi arg="x">A</mi>
  <mi arg="op">T</mn>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="transpose(@*)">
  <mi>A</mi>
  <mi>T</mn>
</msup>
```
{::nomarkdown}
</td>
</tr>

<tr><td> </td><td> adjoint $A^\dagger$ </td>
<td>
{:/nomarkdown}
```
<msup semantic="#op(#x)">
  <mi arg="x">A</mi>
  <mi arg="op" semantic="adjoint">&dagger;</mn>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
Note: 'adjoint' needs t0 know the second arg is the argument. It could just as easily be the first arg if we define it that way.
```
<msup semantic="adjoint(@op, @x)">
  <mi arg="x">A</mi>
  <mi arg="op">&dagger;</mn>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="adjoint(@2, @1)">
  <mi>A</mi>
  <mi>&dagger;</mn>
</msup>
```
{::nomarkdown}
</td>
</tr>
<tr><td> </td><td> $2$-nd derivative $f''$ </td>
<td>
{:/nomarkdown}
```
<msup semantic="derivative-implicit-variable(#op,#n)">
  <mi arg="op">f</mi>
  <mo arg="n" semantic="2">''</mo>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="derivative-implicit-variable(@op,@n)">
  <mi arg="op">f</mi>
  <mo arg="n">''</mo>
</msup>
```
{::nomarkdown}
</td>
<td>
{:/nomarkdown}
```
<msup semantic="derivative-implicit-variable(@*)">
  <mi>f</mi>
  <mo>''</mo>
</msup>
```
{::nomarkdown}
</td>
</tr>
<tr><td>Awkward nesting</td><td> $x'_i$ </td><td>
{:/nomarkdown}
```
 <msubsup semantic="derivative-implicit-variable(index(#array,#index))">
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
 <msubsup semantic="index(derivative-implicit-variable(#op),#index)">
   <mi arg="op">x</mi>
   <mi arg="index">i</mi>
   <mo>'</mo>
  </msubsup>
```
{::nomarkdown}
</td></tr>
<tr><td></td><td> midpoint $\overline{x}_i$ </td><td>
{:/nomarkdown}
```
 <msub semantic="#op(index(#line,#index))">
    <mover accent="true">
      <mi arg="line">x</mi>
      <mo arg="op" semantic="midpoint">¯</mo>
    </mover>
    <mi arg="index">i</mi>
  </msub>
```
{::nomarkdown}
</td></tr>

<!-- ======================================== -->
<tr><td> base-operator </td><td> binomail $C^n_m$ </td><td>
{:/nomarkdown}
```
<msubsup semantic="#op(#n,#m)">
  <mi arg="op" semantic="binomial">C</mi>
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
<mrow semantic="absolute-value(#x)">
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
<mrow semantic="norm(#x)">
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
<mrow semantic="determinant(#x)">
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
<mrow semantic="sequence(#arg)">
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
<mrow semantic="open-interval(#a,#b)">
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
<mrow semantic="open-interval(#a,#b)">
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
<mrow semantic="inner-product(#a,#b)">
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
<mrow semantic="Legendre-symbol(#n,#p)">
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
<mrow semantic="Clebsch-Gordan(#a1,#a2,#a3,#a4,#b1,#b2,#b3,#b4)">
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
<msup semantic="Pochhammer(#a,#n)">
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
<!-- <mrow semantic="binomial(@2/1,@2/2)"> -->
```
<mrow semantic="binomial(#n,#m)">
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
<!-- <mrow semantic="multinomial(@2/1,@2/2/1,@2/2/3,@2/2/5)"> -->
```
<mrow semantic="multinomial(#n,#m1,#m2,#m3)">
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
<mrow semantic="Eulerian-numbers(#n,#k)">
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
<!-- <mrow semantic="3j(@2/1/1,@2/1/2,@2/1/3,@2/2/1,@2/2/2,@2/2/3)">-->
```
<mrow semantic="3j(#j1,#j2,#j3,#m1,#m2,#m3)">
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
<mrow semantic="#op(#p1,#p2,#a1,#q)">
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
<mrow semantic="#op(#nu,#z)">
  <msub>
    <mi arg="op" semantic="BesselJ">J</mi>
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
<mrow semantic="#op(#nu)(#z)">
  <msub>
    <mi arg="op" semantic="BesselJ">J</mi>
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
<!-- <mfrac semantic="Leibnitz-derivative(@1/2,@2/1/2,@1/1/2)"> -->
```
<mfrac semantic="Leibnitz-derivative(#func,#var,#deg)">
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
<mrow semantic="#op(divide(1,#r),#bvar)">
  <mo arg="op" semantic="integral">&x222B;</mo>
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
<tr><td/><td colspan="2">One might be tempted put semantic="divide(1,#r)" on the mfrac, but this blocks access to #bvar</td></tr>
<!-- ======================================== -->
<tr><td>continued fractions</td><td> $a_0+\displaystyle\frac{1}{a_1+\displaystyle\frac{1}{a_2+\cdots}}$</td><td>
{:/nomarkdown}
<!--<mrow semantic="infinite-continued-fraction(@1,1,@3/1/2/1,1,@3/1/2/3/1/2)">-->
```
<mrow semantic="infinite-continued-fraction(#a0,#b1,#a1,#b2,#a2)">
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
              <mo>&#22EF;</mo>
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

## Summary
The main differences appear to be:
* the location of the name of the function

<!-- %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% -->