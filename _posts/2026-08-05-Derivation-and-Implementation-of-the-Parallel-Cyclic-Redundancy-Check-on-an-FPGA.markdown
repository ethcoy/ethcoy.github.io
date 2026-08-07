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

Now, let us consider the state of the CRC circuit $R[n]$ at sample $n$. At the first first sample $n$, the circuit takes on the state


\begin{equation}
X_{m,n} = 
\begin{pmatrix}
  x_{1,1} & x_{1,2} & \cdots & x_{1,n} \\
  x_{2,1} & x_{2,2} & \cdots & x_{2,n} \\
  \vdots  & \vdots  & \ddots & \vdots  \\
  x_{m,1} & x_{m,2} & \cdots & x_{m,n} 
\end{pmatrix}
(\#eq:matex)
\end{equation}





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
