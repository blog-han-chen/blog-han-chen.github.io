---
layout: post
title: "My Understanding of Boyer Moore Algorithm"
description: ""
category: algorithm
tags: [algorithm,string-matching]
---
{% include JB/setup %}
{:center: style="text-align: center"}

Boyer-Moore algorithm is an algorithm that finds pattern P in text T. It tries to do so by comparing P from the back to front. 

I find it difficult to understand one of the heuristics: the good suffix jump. Hopefully this post clarifies a few things. 


Good Suffix Jump
----
Given pattern P: bababobo

*Case 1:*
Suppose that we already matched the last `bo` but failed to match P[i] = o, where i = 5. We find the last position where `bo` occurs elsewhere in P. In that case, it is P[4..5]. Then we re-align the last substring `bo` to the `bo` that is matched. 

~~~
bababobo
     ^ failed
  bababobo
~~~

*Case 2:*
But what if the suffix doesn't appear elsewhere in P? 
Say P = bcabc, we successfully matched the end `abc` in the pattern, but failed at P[i=1] = c. `abc` occurs nowhere else in P. Ideally we'd like to align the first P[0..1] = bc to the last P[3..4]=bc and continue. More abstractly, we'd like to find the longest prefix of P, that matches some suffix of `abc`. 

Then in terms of algorithm, what number should we return in Good Suffix array at each position?
We make the following assumption about our algorithm: 

> By default, if the algorithm failed at matching T[i] to P[j], it is going to restart comparison at T[i+m-1] and P[m-1], where m is the length of the pattern. It is equivalent of aligning the first character in P to the failing position, and restart from the back again. 

Example: we failed matching at the last character c

~~~
abcabc
     abcabc //realign a to c
~~~


The number that we return from Good Suffix array tells the algorithm how far it should go back. Say if it is 1, then we shift the alignment one character back. 

~~~
abcabc
     ^ failed here
     abcabc // default
    abcabc //shift back 1, realign the second character to c
~~~


----------

Now say we failed at the third last charcter o for pattern bababobo
by default the algorithm does this:

~~~
bababobo
     ^ failed here
     bababobo // not correct
~~~

But we want the algorithm to shift back by 3 positions, so that the `bo` that we matched aligns with the last `bo` in P. 

~~~
bababobo
     ^ failed here
  bababobo
~~~

It happens so that the number of positions we wish to shift back is the same as the index just before the substring `bo` that occurs elsewhere in P. In this case, i=3 is the position before `P[4..5]=bo`. 

There is one more detail here that is not mentioned above, suppose we have pattern P=bonobobo, what happens if we align the same way? 

Failing at P[5]=o, aligning the second last `bo` with the last `bo` matched

~~~
bonobobo
     ^ failed here
  bonobobo //oops, we already know that the text failed to match character o 
      bonobobo //should really be this, which is case 2
~~~

So we add the constraint that the P[index to return] != P[index failed to match]

Thus we know what to return for Case 1 of Good Suffix. To summarize:

> Suppose we failed at P[j], but have already matched some suffix of P, say M, return the index i just before the last M occurs in P, but P[i] != P[j]. 

Case 2 is more complicated in terms of what to return, we sometimes need to return negative numbers, but the idea is the same as case 1. Think in terms of the algorithm, how far do we shift back (or forward) if we failed at P[i]? 

http://whocouldthat.be/visualizing-string-matching/ is good resource.




