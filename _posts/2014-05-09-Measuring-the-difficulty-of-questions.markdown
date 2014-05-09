---
title: Measuring the difficulty of questions
layout: post
guid:
comments: true
tags:
  - PCI group
---

1. A competition-based method for measuring question difficulty

Invented as an improved chess rating system, [Elo rating system](http://en.wikipedia.org/wiki/Elo_rating_system) is widely used in games and team sports. Herbrich et al.(http://papers.nips.cc/paper/3331-trueskill-through-time-revisiting-the-history-of-chess.pdf) from Microsoft Research (Cambridge) developed the TrueSkill rating system by combining Elo's method with Bayesian inference and applied it in macthing competitors for Xbox online game (e.g., Halo 2) players [[1]](http://papers.nips.cc/paper/3331-trueskill-through-time-revisiting-the-history-of-chess.pdf). Liu Jing et al. from Harbin Institute of Technology (China) and Microsoft Research (Asia) applied the TrueSkill rating system to estimate the difficulties of questions and the expertise experts in online Q&A communities [[2]](http://aclweb.org/anthology//D/D13/D13-1009.pdf)[[3]](http://dl.acm.org/citation.cfm?id=2009975). They showed that this method overperforms other structure-based analysis such as the page rank of user network [[4]](http://www.ladamic.com/papers/taskcn/YangICWSM2008TaskCn.pdf).


TrueSkill system, assumes that the 'true skill' of each player in a game follows a normal distribution with mean mu and variance sigma. And them the system update these distributions by the competition results. In particular, the system consider the current skill of two players (priori probability) and the winner-loser result (likelihood) to update the player skill levels (posterior probability). 

$$
\mu_{winner} = \mu_{winner} + \frac{\sigma^{2}_{winner}}{c}v(\frac{t}{c}), \,\,\,\,\,   (1)
$$

$$
\mu_{loser} = \mu_{loser} - \frac{\sigma^{2}_{loser}}{c}v(\frac{t}{c}), \,\,\,\,\,   (2)
$$

$$
\sigma_{winner} = \sigma_{winner} [1-\frac{\sigma^{2}_{winner}}{c^2}w(\frac{t}{c})], \,\,\,\,\,   (3)
$$

$$
\sigma_{loser} = \sigma_{loser} [1-\frac{\sigma^{2}_{loser}}{c^2}w(\frac{t}{c})], \,\,\,\,\,   (4)
$$

in which 

$$
t = \mu_{winner} - \mu_{loser}, \,\,\,\,\,   (5)
$$

$$
c^2 = 2\beta^2 + \sigma^{2}_{winner} + \sigma^{2}_{loser}, \,\,\,\,\,   (6)
$$

$$
v(t)= \frac{N(t)}{\Phi(t)}, \,\,\,\,\,   (7)
$$

$$
w(t)= v(t)(v(t)+t), \,\,\,\,\,   (8)
$$

N and Phi in Eq.(7) stand for the PDF and CDF of standardized normal distribution, respectively. 

From the above functions we know that TrueSkill updates the skill level mu and the uncertainty sigma intuitively. If the competition outcome is expected: the player of higher skill level wins, the system will updates mu and sigma slightly in positive direction (Matthew effect). On the contrary, if the outcome is unexpected, i.e. the player of lower skill level wins, the system will updates mu and sigma greatly in negative direction. The following figure visualizes the effects of updating rules and two exmaple results. 


![demo](/media/files/2014-05-09-Measuring-the-difficulty-of-questions/demo.png)



