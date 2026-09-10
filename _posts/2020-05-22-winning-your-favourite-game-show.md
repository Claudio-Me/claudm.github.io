---
title: "Winning Your Favourite Game Show"
date: 2020-05-22
permalink: /posts/2020/05/winning-your-favourite-game-show/
tags:
  - probability
  - game theory
header:
  teaser: "blog/montyhall.jpg"
---

*It is called Monty Hall* problem and describes a very typical situation in TV shows in which you are asked to find your prize among a set of closed boxes.

![Monty Hall](/images/blog/montyhall.jpg)

Suppose you are in a TV show and in front of you there are three identical boxes, you know that only one of them contains a bag of money, the other two are empty. You choose the first one and then out of the remaining two, the host shows you that one of the two is empty. Now only two boxes are still in play and the host asks you a final question: "*Do you want to keep your box or do you want to switch it*?".

The question sounds very naive and most of the people would just choose randomly, but what if I tell you there is one move that allows you to actually increase you chances of winning?

This is one of the basic examples which are usually shown at the start of a probability course and in order to allow the math to give us the right answer, it is very important that the host knows from the beginning where the money is.
I will not bore you with the computation but if you have already encountered the Bayes rule, it is an easy exercise. The probability that the gold is in your chosen box is 1/3, hence the probability that the gold is in the other boxes is 2/3, which is higher. But again we are not interested in the mathematical proof, we want to find a more intuitive explanation.

Now imagine the same example but with one thousand boxes: 999 empty, only one with the money.
Now after you choose your box, the probability that you have already chosen the right box is very low (1/1000). On the other hand the probability that the winning box is among the 999 left is very high. So if you were asked now to choose between the one box you just selected and all the remaining 999 you will choose the remaining 999. And that is exactly what happens. When the host is removing some of the boxes he is just doing some work for you, consciously leaving the winning one closed and in the end, when he asks you if you want to switch or not we can rephrase the question as: *"Do you want to keep your single box or do you prefer to have all the other 999, some of which I already opened for you?"*
Of course this is an extremization but when we use three boxes instead of a thousand the situation is the same, just that the probabilities are a little bit more balanced.

Now I hope this example sufficed in explaining why the best move is to switch the box, but even if you are still struggling to convince yourself, don't worry as you are not the only one. Remember what a Harvard University professor said:

"*Misunderstanding of probability may be the greatest of all impediments to scientific literacy.*" **Gould, Stephen Jay**
