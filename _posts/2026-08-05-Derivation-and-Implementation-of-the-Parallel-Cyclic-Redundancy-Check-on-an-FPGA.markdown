---
layout: post
title:  "Derivation and Implementation of the Parallel Cyclic Redundancy Check on an FPGA"
date:   2026-08-05 20:45:00 -0300
categories: math, fpga
---

* TOC
{:toc}

# Introduction

The Cyclic-Redundancy-Check (CRC) is an algorithm used in, likely, every single modern computer in the world! The purpose of the CRC is to detect if an error
has occurred when sending a message from some source to a receiver.

# Serial Cyclic Redundancy Check

The purpose of this section will be to develop an understanding of the two different serial CRCs...

# Problems with the Serial Cyclic Redundancy Check

The purpose of this section is to discuss the problems of with the serial CRC 

# Derivation of the Parallel Cylic Redundancy Check

To begin our derivation of the parallel CRC, we shall first consider the serial CRC circuit shown below in Figure X.

INSERT FIGURE OF CRC

The serial CRC circuit shown in Figure X has the generator polynomial

$$
g(x) = x^4 + x + 1
$$

Now, let us consider the state of the CRC circuit $R[n]$ at sample $n$. At the first sample $n$, the circuit takes on the state

$$
R[n] = \begin{pmatrix} f_3[n] \\\ f_2[n] \\\ f_1[n] \\\ f_0[n] \end{pmatrix}
$$

Now, let us consider what $R[n]$ is when a rising edge of the driving clock occurs (i.e., what is $R[n + 1]$). Well, this circuit is just a LFSR, so all of the values get shifted, with some modifications depending on the placement of the XORs (denoted by $\oplus$) and the input bit $b[n]$, right? So the state of the CRC circuit after the next rising edge of the clock will be

$$
R[n + 1] = \begin{pmatrix} f_2[n]  \\\ f_1[n] \\\ f_0[n] \oplus f_3[n] \oplus b[n] \\\ f_3[n] \oplus b[n] \end{pmatrix}
$$

So, after a rising edge of the clock occurs, this is the relationship that the next state of the CRC circuit has with its initial state. Let's break this matrix up into the sum of a matrix dependent on the states of the flip-flops and another dependent on the bit input into the circuit.

$$
R[n + 1] = \begin{pmatrix} f_2[n]  \\\ f_1[n] \\\ f_0[n] \oplus f_3[n] \\\ f_3[n] \end{pmatrix} \oplus \begin{pmatrix} 0 \\\ 0 \\\ 1 \\\ 1 \end{pmatrix} b[n]
$$

Next, let us factor the above equation so the matrix dependent on the state of the flip-flops into the form of some coefficient matrix $A$ multiplied by $R[n]$

$$
R[n + 1] = 
\begin{pmatrix}
  0 & 1 & 0 & 0 \\
  0 & 0 & 1 & 0 \\
  1 & 0 & 0 & 1 \\
  1 & 0 & 0 & 0
\end{pmatrix}
\begin{pmatrix}
  f_3[n] \\
  f_2[n] \\
  f_1[n] \\
  f_0[n]
\end{pmatrix} \oplus
\begin{pmatrix}
  0 \\
  0 \\
  1 \\
  1
\end{pmatrix} b[n]
$$

Notice anything about the first column of the matrix $A$ and the column matrix multiplied by $b[n]$? Well, for one, they are both the same. However, what is even more exciting is that they are the generator polynomial in matrix form! That is, our generator polynomial is given by

$$
g(x) = x^4 + x + 1
$$

Which, if we were to represent this as a matrix, it would be

$$
G' = \begin{pmatrix} 1  \\\ 0 \\\ 0 \\\ 1 \\\ 1 \end{pmatrix}
$$

Or, in general, for a CRC generator polynomial CRC-N that has an order of N

$$
G' = 
\begin{pmatrix}
  g_{n} \\
  g_{n - 1} \\
  \vdots \\
  g_{1} \\
  g_{0}
\end{pmatrix}
$$

From here, you can see that $R[n + 1]$ is a function of $G$ where

$$
G = 
\begin{pmatrix}
  0 \\
  0 \\
  1 \\
  1
\end{pmatrix}
$$

Or, in general

$$
G = 
\begin{pmatrix}
  g_{n - 1} \\
  g_{n - 2} \\
  \vdots \\
  g_{1} \\
  g_{0}
\end{pmatrix}
$$

So, this means that the next state of the CRC circuit after the rising edge of the clock is

$$
R[n + 1] = A R[n] \oplus G b[n]
$$

where

$$
A = 
\begin{pmatrix}
  0 & 1 & 0 & 0 \\
  0 & 0 & 1 & 0 \\
  1 & 0 & 0 & 1 \\
  1 & 0 & 0 & 0
\end{pmatrix}, \ R[n] = 
\begin{pmatrix}
  f_3[n] \\
  f_2[n] \\
  f_1[n] \\
  f_0[n]
\end{pmatrix}, \ \text{and} \ G =
\begin{pmatrix}
  0 \\
  0 \\
  1 \\
  1
\end{pmatrix}
$$






You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

Jekyll requires blog post files to be named according to the following format:

`YEAR-MONTH-DAY-title.MARKUP`

Where `YEAR` is a four-digit number, `MONTH` and `DAY` are both two-digit numbers, and `MARKUP` is the file extension representing the format used in the file. After that, include the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
