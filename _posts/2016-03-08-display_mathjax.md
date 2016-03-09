---
published: true
title: Display math equations and symbols
category: MathJax
tags: 
  - math
layout: post
---

先来几个较复杂的数学公式：


$$ \Psi(z) \ge \Psi(\tilde{w}) + \nabla \Psi(\tilde{w})^\top (z-\tilde{w}) + \frac{\mu}{2}\|z-\tilde{w}\|^2 ~. $$
 
 
$$ P(z) \ge P(w^+) + \frac{\rho}{2}\|w^+-y\|^2 -\left(1+\frac{\rho}{\mu}\right)\left(P(w^+;y) ~. $$

$$\begin{align*}
P(z) &=  \Psi(z) + \frac{\mu}{2} \|y\|^2 + \mu \,y^\top (z-y) +
\frac{\mu}{2} \|z-y\|^2 \\
&\ge \Psi(\tilde{w}) + \nabla \Psi(\tilde{w})^\top (z-\tilde{w}) +
\frac{\mu}{2}\|z-\tilde{w}\|^2 +  \frac{\mu}{2} \|y\|^2 + \mu \,y^\top (z-y) +
\frac{\mu}{2} \|z-y\|^2 \\
&= P(\tilde{w};y) - \frac{\rho}{2}\|\tilde{w}-y\|^2+ \nabla \Psi(\tilde{w})^\top (z-\tilde{w}) 
+ \mu \,y^\top (z-\tilde{w})  + \frac{\mu}{2}\left(\|z-\tilde{w}\|^2 +\|z-y\|^2 \right) \\
&= P(\tilde{w};y) - \frac{\rho}{2}\|\tilde{w}-y\|^2 + \rho(y-\tilde{w})^\top(z-\tilde{w}) + \frac{\mu}{2}\left(\|z-\tilde{w}\|^2 +\|z-y\|^2 \right) \\
&= P(\tilde{w};y) + \frac{\rho}{2}\|\tilde{w}-y\|^2 + \rho(y-\tilde{w})^\top(z-y) +
\frac{\mu}{2}\left(\|z-\tilde{w}\|^2 + \|z-y\|^2 \right) ~.
\end{align*}
$$

\\[ \mathbf{X} = \mathbf{Z} \mathbf{P^\mathsf{T}} \\]

\\[ \mathbf{X} \_{n,p} = \mathbf{A} \_{n,k} \mathbf{B} \_{k,p} \\]

$$ \mathsf{Data = PCs} \times \mathsf{Loadings} $$

$$ \min_{w \in \mathbf{R}^d} P(w) ~~~~\textrm{where}~~~~ P(w) = \left[ \frac{1}{n} \sum_{i=1}^n \phi_i( X_i^\top w) + \lambda g(w) \right]. $$

$$ \mathbb{E}_0[P_t]=\exp\Big(f(t)+(\ln P_0-f(0))e^{-\kappa t}+\frac{\sigma^2}{4\kappa}(1-e^{-2\kappa t}) \Big) $$

$$ d Y_t=-\kappa Y_t dt+\sigma d W_t $$

$$ \ln F(t,T)=f(T)+e^{-\kappa(T-t)}(\ln P_t-f(t))+\alpha^*(1-e^{-\kappa(T-t)})+\frac{\sigma^2}{4\kappa}(1-e^{-2\kappa(T-t)}) $$

$$ \begin{align*}
  &O\left(d\left(n+
      \min\left\{\frac{1}{\lambda\,\gamma},\sqrt{\frac{n}{\lambda\,\gamma}}\right\}\right)\log(1/\epsilon)\,
    \log(1/\delta)\,\max\{1,\log^2(1/(\lambda\,\gamma\,n))\}\right) \\
  &~~=~~ \tilde{O}\left(d\left(n+
      \min\left\{\frac{1}{\lambda\,\gamma},\sqrt{\frac{n}{\lambda\,\gamma}}\right\}\right)\right)
  ~.
\end{align*} 
$$

$$a^2 + b^2 = c^2$$

\\[ J_\alpha(x) = \sum_{m=0}^\infty \frac{(-1)^m}{m! \Gamma (m + \alpha + 1)} {\left({ \frac{x}{2} }\right)}^{2m + \alpha} \\]

$$ J_\alpha(x) = \sum_{m=0}^\infty \frac{(-1)^m}{m! \Gamma (m + \alpha + 1)} {\left({ \frac{x}{2} }\right)}^{2m + \alpha} $$

$$ x^{y^z}=(1+{\rm e}^x)^{-2xy^w} $$

$$ \vec{a} \cdot \vec{b}=0 $$

总结一下希腊字母：

$$ \alpha  \beta　\gamma　\Gamma　\delta　\Delta　\epsilon \varepsilon　　\zeta　\eta　\theta　\Theta　\vartheta \iota　\kappa $$

$$　\lambda　\Lambda　\mu　　\nu　\xi　\Xi　　\pi　\Pi　\varpi　　\rho　\varrho　　\sigma　\Sigma　\varsigma　　\tau　$$

$$　\upsilon　\Upsilon  \phi　\Phi　\varphi　\chi　　\psi　\Psi　\omega　\Omega $$

箭头：

$$  \uparrow  \downarrow  \Uparrow \Downarrow  \rightarrow  \leftarrow  \Rightarrow $$

$$  \Leftarrow  \longrightarrow  \longleftarrow  \Longrightarrow  \Longleftarrow $$

划线：

$$ \overline{a+b+c+d} $$  $$\underline{a+b+c+d} $$  $$ \overbrace{a+\underbrace{b+c}_{1.0}+d}^{2.0} $$

戴帽子：

$$ \hat{y}  \check{y}  \breve{y} $$

运算符号：

$$ \because  \therefore  \forall  \exists \not=  \not> \not\subset $$

$$ \prime  \int  \iint  \iiint  \iiiint  \oint  \lim  \infty  \nabla $$

$$ \pm $$  $$ \times $$  $$ \div $$  $$ \mid $$  $$ \nmid $$  $$ \cdot $$  $$ \circ $$

$$ \ast $$  $$ \bigodot $$  $$ \bigotimes $$  $$ \bigoplus $$ $$\leq $$ $$ \geq $$

$$ \neq $$  $$ \approx $$  $$ \equiv $$  $$ \sum $$  $$ \prod $$  $$ \coprod $$

集合运算符：

$$ \emptyset $$  $$ \in $$  $$ \notin $$  $$ \subset $$  $$ \supset $$  $$ \subseteq $$ $$ \supseteq $$

$$ \bigcap $$  $$ \bigcup $$  $$ \bigvee $$  $$ \bigwedge $$ $$ \biguplus $$ $$ \bigsqcup $$

对数运算符：

$$ \log $$  $$ \lg $$  $$ \ln $$

三角运算符：

$$ \bot $$  $$ \angle $$  $$ 30^\circ $$  $$ \sin $$  $$ \cos $$ 

$$ \tan $$  $$ \cot $$  $$ \sec $$  $$ \csc $$


