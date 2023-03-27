Estimating Pi With a Shotgun
================
Isabella Abilheira
2023

- <a href="#grading-rubric" id="toc-grading-rubric">Grading Rubric</a>
  - <a href="#individual" id="toc-individual">Individual</a>
  - <a href="#due-date" id="toc-due-date">Due Date</a>
- <a href="#monte-carlo" id="toc-monte-carlo">Monte Carlo</a>
  - <a href="#theory" id="toc-theory">Theory</a>
  - <a href="#implementation" id="toc-implementation">Implementation</a>
    - <a
      href="#q1-pick-a-sample-size-n-and-generate-n-points-uniform-randomly-in-the-square-x-in-0-1-and-y-in-0-1-create-a-column-stat-whose-mean-will-converge-to-pi"
      id="toc-q1-pick-a-sample-size-n-and-generate-n-points-uniform-randomly-in-the-square-x-in-0-1-and-y-in-0-1-create-a-column-stat-whose-mean-will-converge-to-pi"><strong>q1</strong>
      Pick a sample size <span class="math inline"><em>n</em></span> and
      generate <span class="math inline"><em>n</em></span> points <em>uniform
      randomly</em> in the square <span
      class="math inline"><em>x</em> ∈ [0,1]</span> and <span
      class="math inline"><em>y</em> ∈ [0,1]</span>. Create a column
      <code>stat</code> whose mean will converge to <span
      class="math inline"><em>π</em></span>.</a>
    - <a href="#q2-using-your-data-in-df_q1-estimate-pi"
      id="toc-q2-using-your-data-in-df_q1-estimate-pi"><strong>q2</strong>
      Using your data in <code>df_q1</code>, estimate <span
      class="math inline"><em>π</em></span>.</a>
- <a href="#quantifying-uncertainty"
  id="toc-quantifying-uncertainty">Quantifying Uncertainty</a>
  - <a
    href="#q3-using-a-clt-approximation-produce-a-confidence-interval-for-your-estimate-of-pi-make-sure-you-specify-your-confidence-level-does-your-interval-include-the-true-value-of-pi-was-your-chosen-sample-size-sufficiently-large-so-as-to-produce-a-trustworthy-answer"
    id="toc-q3-using-a-clt-approximation-produce-a-confidence-interval-for-your-estimate-of-pi-make-sure-you-specify-your-confidence-level-does-your-interval-include-the-true-value-of-pi-was-your-chosen-sample-size-sufficiently-large-so-as-to-produce-a-trustworthy-answer"><strong>q3</strong>
    Using a CLT approximation, produce a confidence interval for your
    estimate of <span class="math inline"><em>π</em></span>. Make sure you
    specify your confidence level. Does your interval include the true value
    of <span class="math inline"><em>π</em></span>? Was your chosen sample
    size sufficiently large so as to produce a trustworthy answer?</a>
- <a href="#references" id="toc-references">References</a>

*Purpose*: Random sampling is extremely powerful. To build more
intuition for how we can use random sampling to solve problems, we’ll
tackle what—at first blush—doesn’t seem appropriate for a random
approach: estimating fundamental deterministic constants. In this
challenge you’ll work through an example of turning a deterministic
problem into a random sampling problem, and practice quantifying
uncertainty in your estimate.

<!-- include-rubric -->

# Grading Rubric

<!-- -------------------------------------------------- -->

Unlike exercises, **challenges will be graded**. The following rubrics
define how you will be graded, both on an individual and team basis.

## Individual

<!-- ------------------------- -->

| Category    | Needs Improvement                                                                                                | Satisfactory                                                                                                               |
|-------------|------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| Effort      | Some task **q**’s left unattempted                                                                               | All task **q**’s attempted                                                                                                 |
| Observed    | Did not document observations, or observations incorrect                                                         | Documented correct observations based on analysis                                                                          |
| Supported   | Some observations not clearly supported by analysis                                                              | All observations clearly supported by analysis (table, graph, etc.)                                                        |
| Assessed    | Observations include claims not supported by the data, or reflect a level of certainty not warranted by the data | Observations are appropriately qualified by the quality & relevance of the data and (in)conclusiveness of the support      |
| Specified   | Uses the phrase “more data are necessary” without clarification                                                  | Any statement that “more data are necessary” specifies which *specific* data are needed to answer what *specific* question |
| Code Styled | Violations of the [style guide](https://style.tidyverse.org/) hinder readability                                 | Code sufficiently close to the [style guide](https://style.tidyverse.org/)                                                 |

## Due Date

<!-- ------------------------- -->

All the deliverables stated in the rubrics above are due **at midnight**
before the day of the class discussion of the challenge. See the
[Syllabus](https://docs.google.com/document/d/1qeP6DUS8Djq_A0HMllMqsSqX3a9dbcx1/edit?usp=sharing&ouid=110386251748498665069&rtpof=true&sd=true)
for more information.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.4.0      ✔ purrr   1.0.1 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.1      ✔ stringr 1.5.0 
    ## ✔ readr   2.1.3      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

*Background*: In 2014, some crazy Quebecois physicists estimated $\pi$
with a pump-action shotgun\[1,2\]. Their technique was based on the
*Monte Carlo method*, a general strategy for turning deterministic
problems into random sampling.

# Monte Carlo

<!-- -------------------------------------------------- -->

The [Monte Carlo
method](https://en.wikipedia.org/wiki/Monte_Carlo_method) is the use of
randomness to produce approximate answers to deterministic problems. Its
power lies in its simplicity: So long as we can take our deterministic
problem and express it in terms of random variables, we can use simple
random sampling to produce an approximate answer. Monte Carlo has an
[incredible
number](https://en.wikipedia.org/wiki/Monte_Carlo_method#Applications)
of applications; for instance Ken Perlin won an [Academy
Award](https://en.wikipedia.org/wiki/Perlin_noise) for developing a
particular flavor of Monte Carlo for generating artificial textures.

I remember when I first learned about Monte Carlo, I thought the whole
idea was pretty strange: If I have a deterministic problem, why wouldn’t
I just “do the math” and get the right answer? It turns out “doing the
math” is often hard—and in some cases an analytic solution is simply not
possible. Problems that are easy to do by hand can quickly become
intractable if you make a slight change to the problem formulation.
Monte Carlo is a *general* approach; so long as you can model your
problem in terms of random variables, you can apply the Monte Carlo
method. See Ref. \[3\] for many more details on using Monte Carlo.

In this challenge, we’ll tackle a deterministic problem (computing
$\pi$) with the Monte Carlo method.

## Theory

<!-- ------------------------- -->

The idea behind estimating $\pi$ via Monte Carlo is to set up a
probability estimation problem whose solution is related to $\pi$.
Consider the following sets: a square with side length one $St$, and a
quarter-circle $Sc$.

``` r
## NOTE: No need to edit; this visual helps explain the pi estimation scheme
tibble(x = seq(0, 1, length.out = 100)) %>%
  mutate(y = sqrt(1 - x^2)) %>%

  ggplot(aes(x, y)) +
  annotate(
    "rect",
    xmin = 0, ymin = 0, xmax = 1, ymax = 1,
    fill = "grey40",
    size = 1
  ) +
  geom_ribbon(aes(ymin = 0, ymax = y), fill = "coral") +
  geom_line() +
  annotate(
    "label",
    x = 0.5, y = 0.5, label = "Sc",
    size = 8
  ) +
  annotate(
    "label",
    x = 0.8, y = 0.8, label = "St",
    size = 8
  ) +
  scale_x_continuous(breaks = c(0, 1/2, 1)) +
  scale_y_continuous(breaks = c(0, 1/2, 1)) +
  theme_minimal() +
  coord_fixed()
```

    ## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
    ## ℹ Please use `linewidth` instead.

![](c07-monte-carlo-assignment_files/figure-gfm/vis-areas-1.png)<!-- -->

The area of the set $Sc$ is $\pi/4$, while the area of $St$ is $1$. Thus
the probability that a *uniform* random variable over the square lands
inside $Sc$ is the ratio of the areas, that is

$$\mathbb{P}_{X}[X \in Sc] = (\pi / 4) / 1 = \frac{\pi}{4}.$$

This expression is our ticket to estimating $\pi$ with a source of
randomness: If we estimate the probability above and multiply by $4$,
we’ll be estimating $\pi$.

## Implementation

<!-- ------------------------- -->

Remember in `e-stat02-probability` we learned how to estimate
probabilities as the limit of frequencies. Use your knowledge from that
exercise to generate Monte Carlo data.

### **q1** Pick a sample size $n$ and generate $n$ points *uniform randomly* in the square $x \in [0, 1]$ and $y \in [0, 1]$. Create a column `stat` whose mean will converge to $\pi$.

*Hint*: Remember that the mean of an *indicator function* on your target
set will estimate the probability of points landing in that area (see
`e-stat02-probability`). Based on the expression above, you’ll need to
*modify* that indicator to produce an estimate of $\pi$.

``` r
## TASK: Choose a sample size and generate samples
n <- 10000

#Generate Data
df_q1 <- tibble(
  x = runif(n, min = 0, max = 1), 
  y = runif(n, min = 0, max = 1)
)

df_q1
```

    ## # A tibble: 10,000 × 2
    ##         x     y
    ##     <dbl> <dbl>
    ##  1 0.559  0.986
    ##  2 0.558  0.541
    ##  3 0.0482 0.308
    ##  4 0.0507 0.212
    ##  5 0.525  0.565
    ##  6 0.482  0.879
    ##  7 0.539  0.271
    ##  8 0.945  0.166
    ##  9 0.0664 0.806
    ## 10 0.590  0.930
    ## # … with 9,990 more rows

``` r
df_q1 %>% 
ggplot() +
  geom_point(aes(x, y))
```

![](c07-monte-carlo-assignment_files/figure-gfm/q1-task-1.png)<!-- -->

``` r
df_q1 <- df_q1 %>%
  select(x, y) %>% 
  mutate(stat = (x^2 + y^2 <= 1)) %>% 
  summarize(mean = mean(stat)*4, sd = sd(stat)*4)

df_q1
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.14  1.64

``` r
set.seed(101)

q99 <- qnorm( 1 - (1 - 0.99) / 2 )

df_q1_sampled <- 
  map_dfr(
  c(10, 100, 1000, 1e4, 1e5, 1e6, 1e7, 1e8),
  function(n_samples) {
   tibble(
    x = runif(n_samples, min = 0, max = 1), 
    y = runif(n_samples, min = 0, max = 1)
  ) %>% 
  mutate(stat = (x^2 + y^2 <= 1)) %>% 
  summarize(
    count_total = n(), 
    mean = mean(stat)*4, 
    sd = sd(stat)*4
    ) %>% 
  mutate( 
    lo = mean - q99 * sd / sqrt(n_samples),
    hi = mean + q99 * sd / sqrt(n_samples)
    )
  }
)

df_q1_sampled
```

    ## # A tibble: 8 × 5
    ##   count_total  mean    sd    lo    hi
    ##         <int> <dbl> <dbl> <dbl> <dbl>
    ## 1          10  2.8   1.93  1.23  4.37
    ## 2         100  2.8   1.84  2.33  3.27
    ## 3        1000  3.11  1.66  2.98  3.25
    ## 4       10000  3.14  1.64  3.10  3.18
    ## 5      100000  3.14  1.64  3.13  3.16
    ## 6     1000000  3.14  1.64  3.14  3.15
    ## 7    10000000  3.14  1.64  3.14  3.14
    ## 8   100000000  3.14  1.64  3.14  3.14

### **q2** Using your data in `df_q1`, estimate $\pi$.

``` r
## TASK: Estimate pi using your data from q1
pi_est <- df_q1$mean
pi_est
```

    ## [1] 3.1388

# Quantifying Uncertainty

<!-- -------------------------------------------------- -->

You now have an estimate of $\pi$, but how trustworthy is that estimate?
In `e-stat06-clt` we discussed *confidence intervals* as a means to
quantify the uncertainty in an estimate. Now you’ll apply that knowledge
to assess your $\pi$ estimate.

### **q3** Using a CLT approximation, produce a confidence interval for your estimate of $\pi$. Make sure you specify your confidence level. Does your interval include the true value of $\pi$? Was your chosen sample size sufficiently large so as to produce a trustworthy answer?

``` r
q99 <- qnorm( 1 - (1 - 0.99) / 2 )

df_clt <- df_q1 %>% 
  mutate(
    se = sd / sqrt(n),
    lo = mean - q99 * se,
    hi = mean + q99 * se
  ) 
df_clt
```

    ## # A tibble: 1 × 5
    ##    mean    sd     se    lo    hi
    ##   <dbl> <dbl>  <dbl> <dbl> <dbl>
    ## 1  3.14  1.64 0.0164  3.10  3.18

``` r
## NOTE: No need to change this!
set.seed(101)


# Visualize
df_q1_sampled %>%
  ggplot(aes(count_total, mean)) +
   geom_hline(
    data = df_q1,
    mapping = aes(yintercept = mean),
    size = 0.1
  ) +
 
  geom_errorbar(aes(
    ymin = lo,
    ymax = hi,
    color = (lo <= pi) & (pi <= hi)
  )) +
  geom_point() +
  scale_x_log10() +
  scale_color_discrete(name = "Is pi within the confidence interval?") +
  theme(legend.position = "bottom") +
  labs(
    x = "Samples",
    y = "Mean",
    title = "Estimations of Pi"
  )
```

![](c07-monte-carlo-assignment_files/figure-gfm/confidence%20intervals-1.png)<!-- -->

``` r
## NOTE: No need to change this!
set.seed(101)


# Visualize
tail(df_q1_sampled, 4) %>%
  ggplot(aes(count_total, mean)) +
   geom_hline(
    data = df_q1,
    mapping = aes(yintercept = pi),
    size = 0.1
  ) +
 
  geom_errorbar(aes(
    ymin = lo,
    ymax = hi,
    color = (lo <= pi) & (pi <= hi)
  )) +
  geom_point() +
  scale_x_log10() +
  scale_color_discrete(name = "Is pi within the confidence interval?") +
  theme(legend.position = "bottom") +
  labs(
    x = "Samples",
    y = "Mean",
    title = "Estimations of Pi"
  )
```

![](c07-monte-carlo-assignment_files/figure-gfm/confidence%20intervals%202-1.png)<!-- -->

**Observations**:

- Does your interval include the true value of $\pi$?
  - Yes, the interval is \~3.095 - \~3.16
- What confidence level did you choose?
  - 99%
- Was your sample size $n$ large enough? Why do you say that?
  - Yes, my initial sample size of 10000 was larger enough because the
    confidence interval consistently contained the value of $\pi$.
  - The lower the value of n, the larger the confidence interval is.

# References

<!-- -------------------------------------------------- -->

\[1\] Dumoulin and Thouin, “A Ballistic Monte Carlo Approximation of Pi”
(2014) ArXiv, [link](https://arxiv.org/abs/1404.1499)

\[2\] “How Mathematicians Used A Pump-Action Shotgun to Estimate Pi”,
[link](https://medium.com/the-physics-arxiv-blog/how-mathematicians-used-a-pump-action-shotgun-to-estimate-pi-c1eb776193ef)

\[3\] Art Owen “Monte Carlo”,
[link](https://statweb.stanford.edu/~owen/mc/)
