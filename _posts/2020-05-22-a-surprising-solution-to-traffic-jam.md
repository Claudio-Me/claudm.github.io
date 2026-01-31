---
title: "A Surprising Solution to Traffic Jam"
date: 2020-05-22
permalink: /posts/2020/05/a-surprising-solution-to-traffic-jam/
tags:
  - game theory
  - paradox
header:
  teaser: "blog/braess_first_road_example.png"
---

*It is called Braess's Paradox and it may be the solution to the traffic in your city.*

A common belief is that the more options we have the better our lives will be, but is it true?
Some of you are probably already turning up your nose thinking about the times you had to spend hours or even days trying to choose the jacket or the laptop that suits you better among the infinite possibilities.

Let me simplify the situation even more: If you were a pendular worker would you be happy if a new street is built on you daily commute to work?
Most of you will immediately answer yes, but luckily for those who are more environmentally aware, math does not always agree with what the intuition would suggest.
Indeed the German mathematician **Dietrich Braess** proved that there are situation in which building new roads would actually deteriorate the traffic condition.
The German mathematician proposed this model in the 1968 and it quickly become one of the most famous examples in the field of Game Theory, let us see how it works.

Imagine a city of 4,000 inhabitants where every day citizen have to move from the residential area to the industrial area.
In order to reach their destination each person can choose between two different ways: one that goes trough the mountains (M) and the other that goes through the canyon (C).
In each possible route there are two segment with different times of travel as shown in figure:

- the *wide road* which the travel time is constant (45 minutes)
- the *narrow road* where the travel time depends on the number of drivers D on it (D/100 minutes).

![Road network without highway](/images/blog/braess_first_road_example.png)

In this model we make the reasonable assumption that each driver will always choose the fastest possible route.
With this hypothesis the equilibrium will be reached when half of the people drive through the mountains and half through the canyon. With this traffic flow, since both of the narrow streets are covered by 2,000 cars, all drivers will take the same amount of time to reach the workplace that is (45 + 2,000/100)=**65 minutes**.
Notice that now for a commuter there is no way to find a faster route.
This situation where nobody has incentive to change their strategy is called as **Nash equilibrium** and it is a pivotal quantity in the field of game theory.

Suppose now we **add a new highway** between the mountains (M) and the canyon (C). This highway is so efficient that it takes only 0 minutes to go through.
Let's see how our drivers will react.

![Road network with highway](/images/blog/braess_paradox_road_example.png)

The new equilibrium is reached when all drivers take first the road to the mountains (4,000/100 minutes) then the new highway (0 minutes) and finally use the street from the canyon to the workplace (4,000/100 minutes).
In the end, with this new highway, the travel time for each person will be **80 minutes** which is much higher than before!
Notice also that the previous traffic flow is no more an equilibrium state because the one driver who take the highway would take less time to reach the workplace.

This result is really surprising and mathematically speaking it is justified because the new network has a less efficient Nash equilibrium, but from a more social point of view we can attribute this result to the extremely selfish way of how the drivers think.
This model shows us how in some particular situations leaving people free from any regulations could actually degrade the situation not only for the community but also for the single person. It is not a coincidence that this problem is strictly connected with the so-called "price of anarchy".

Reading this example one could think it is made ad hoc to get this result especially since we are working with just a small number of drivers.
But actually there are a lot of traffic jam situations even in the biggest cities of the world that can be solved by this model.
Some of those are New York with the closure of the 42nd street, Stuttgart in Germany and London, but probably the most spectacular case is Seoul with the **Cheonggyecheon restoration project**.
Here the Metropolitan Government decided to reduce the 10-lane roadway and the 4-lane elevated highway to restore the river Cheonggyecheon and create a new park in the middle of the city. Of course in the beginning the project countered a lot of opposition but in the end the public had to recognize the value of the work.

Maybe if people were better at math, we would have more parks like this.

![Cheonggyecheon park](/images/blog/cheonggyecheon_stream.jpg)
*Cheonggyecheon park*
