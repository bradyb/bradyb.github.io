---
layout: post
title:  "Pythagorean Basketball"
date:   2022-05-28 2:00:00 -0800
categories: blog nba
usemathjax: true
---

In various sports, we have these [Pythagorean Expectations](https://en.wikipedia.org/wiki/Pythagorean_expectation)
that try to say how what a teams winning percentage should be given the points they've allowed and the points they've
scored so far this season.

They generally look like 

$$ \mathbb{E}[W_{team}]_q = \frac{p_s^q}{p_a^q + p_s^q} $$

where $$\mathbb{E}[W_{team}]$$ is a team's expected winning percentage, $$p_s$$ and $$p_a$$ are the team's total points scored and
allowed, respectively. And $$q$$ is some exponent that differs by the sport.

From what I can tell in baseketball, this exponent $$q$$ is taken to either be 13.91 (according to [Daryl Morey](https://en.wikipedia.org/wiki/Daryl_Morey))
or 16.5 (according to [John Hollinger](https://en.wikipedia.org/wiki/John_Hollinger)). Which one should we be using?

I think I have an idea how Daryl Morey got to 13.91 - I came to a similar result by finding the best exponent that makes the
pythagorean expectation fit historical NBA data.

On Kaggle, you can conveniently find a [CSV](https://www.kaggle.com/datasets/nathanlauga/nba-games) that contains data about every
NBA game dating back to the 2004 season. Using this we can try to minimize the RMSE:

$$ e(q) = \sqrt{\frac{\sum_{season}\sum_{team} (A_{team, season} - \mathbb{E}[W_{team}]_q)^2}{N}} $$

where $$A_{team, season}$$ is a team's actual winning percentage for a season and $$N=509$$ is total number of team seasons in the Kaggle data. 

[I used](https://github.com/bradyb/basketball-pythagorean/blob/master/src/find_optimal_exponent.py) ```scipy.optimize.minimize``` to find 
the $$q$$ that minimizes $$e(q)$$ with initial guesses spanning from 2 to 30 and each minimization returned $$ q = 13.82 $$.

Note that $$ e(16.5) = 0.041866 $$ and $$ e(13.82) = 0.035271 $$.

My hypothesis is that Daryl performed a similar computation, but instead of using the RMSE, he used the absolute difference:

$$ e_{abs}(q) = \sum_{season}\sum_{team} |A_{team, season} - \mathbb{E}[W_{team}]_q| $$

Using this error function does result in $$q=13.91$$ on the Kaggle data set.