---
output: hugodown::md_document
title: "S04E15: R for Data Science - Exploratory Data Analysis"
subtitle: "Chapter 7.3: Plotting the distributions of categorical and continuous variables"
summary: "This chapter covers so-called Exploratory Data Analysis (EDA): computing summary stats and especially making quick plots to explore the variation in and distributions of single variables (this session), and looking at covariation among variables (next session)."
authors: [admin]
tags: [codeclub, r4ds]
date: 2022-11-15
lastmod: 2022-11-15
toc: true
rmd_hash: 47b87bd1a989377c

---

------------------------------------------------------------------------

## Setting up

To start with, we'll only need to load the *tidyverse*, as we'll explore a dataset that is automatically loaded along with it.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'>## You only need to install if you haven't previously done so</span></span>
<span><span class='c'># install.packages("tidyverse")</span></span>
<span></span>
<span><span class='kr'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='o'>(</span><span class='nv'><a href='https://tidyverse.tidyverse.org'>tidyverse</a></span><span class='o'>)</span></span>
<span><span class='c'>#&gt; ── <span style='font-weight: bold;'>Attaching packages</span> ─────────────────────────────────────── tidyverse 1.3.2 ──</span></span>
<span><span class='c'>#&gt; <span style='color: #00BB00;'>✔</span> <span style='color: #0000BB;'>ggplot2</span> 3.3.6      <span style='color: #00BB00;'>✔</span> <span style='color: #0000BB;'>purrr  </span> 0.3.5 </span></span>
<span><span class='c'>#&gt; <span style='color: #00BB00;'>✔</span> <span style='color: #0000BB;'>tibble </span> 3.1.8      <span style='color: #00BB00;'>✔</span> <span style='color: #0000BB;'>dplyr  </span> 1.0.10</span></span>
<span><span class='c'>#&gt; <span style='color: #00BB00;'>✔</span> <span style='color: #0000BB;'>tidyr  </span> 1.2.1      <span style='color: #00BB00;'>✔</span> <span style='color: #0000BB;'>stringr</span> 1.4.1 </span></span>
<span><span class='c'>#&gt; <span style='color: #00BB00;'>✔</span> <span style='color: #0000BB;'>readr  </span> 2.1.3      <span style='color: #00BB00;'>✔</span> <span style='color: #0000BB;'>forcats</span> 0.5.2 </span></span>
<span><span class='c'>#&gt; ── <span style='font-weight: bold;'>Conflicts</span> ────────────────────────────────────────── tidyverse_conflicts() ──</span></span>
<span><span class='c'>#&gt; <span style='color: #BB0000;'>✖</span> <span style='color: #0000BB;'>dplyr</span>::<span style='color: #00BB00;'>filter()</span> masks <span style='color: #0000BB;'>stats</span>::filter()</span></span>
<span><span class='c'>#&gt; <span style='color: #BB0000;'>✖</span> <span style='color: #0000BB;'>dplyr</span>::<span style='color: #00BB00;'>lag()</span>    masks <span style='color: #0000BB;'>stats</span>::lag()</span></span></code></pre>

</div>

We'll be working with the `diamonds` dataset today, so let's take a quick look at it before we begin:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>diamonds</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 53,940 × 10</span></span></span>
<span><span class='c'>#&gt;    carat cut       color clarity depth table price     x     y     z</span></span>
<span><span class='c'>#&gt;    <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;ord&gt;</span>     <span style='color: #555555; font-style: italic;'>&lt;ord&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;ord&gt;</span>   <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;int&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span></span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 1</span>  0.23 Ideal     E     SI2      61.5    55   326  3.95  3.98  2.43</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 2</span>  0.21 Premium   E     SI1      59.8    61   326  3.89  3.84  2.31</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 3</span>  0.23 Good      E     VS1      56.9    65   327  4.05  4.07  2.31</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 4</span>  0.29 Premium   I     VS2      62.4    58   334  4.2   4.23  2.63</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 5</span>  0.31 Good      J     SI2      63.3    58   335  4.34  4.35  2.75</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 6</span>  0.24 Very Good J     VVS2     62.8    57   336  3.94  3.96  2.48</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 7</span>  0.24 Very Good I     VVS1     62.3    57   336  3.95  3.98  2.47</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 8</span>  0.26 Very Good H     SI1      61.9    55   337  4.07  4.11  2.53</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 9</span>  0.22 Fair      E     VS2      65.1    61   337  3.87  3.78  2.49</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>10</span>  0.23 Very Good H     VS1      59.4    61   338  4     4.05  2.39</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># … with 53,930 more rows</span></span></span></code></pre>

</div>

On each row, we have information about one individual diamond, such as its `carat` and `price`. (`x`, `y`, and `z` represent the diamond's length, width, and depth, respectively.)

Since we'll be making a bunch of plots with *ggplot2*, let's use the following trick to set an overarching "theme" for all plots that is a little better-looking than the default one:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'># This changes two things:</span></span>
<span><span class='c'># - theme_minimal() gives an overall different look, with a white background</span></span>
<span><span class='c'># - base_size = 14 will make the text relatively bigger</span></span>
<span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/theme_get.html'>theme_set</a></span><span class='o'>(</span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='o'>(</span>base_size <span class='o'>=</span> <span class='m'>14</span><span class='o'>)</span><span class='o'>)</span></span></code></pre>

</div>

<br>

------------------------------------------------------------------------

## Chapter 7.3: Variation

### Exploring variation in a categorical variable

Let's say we want to see how many diamonds there are for each value of `cut`. When we printed the first lines of the dataframe above, we could see that `cut` has values like `Ideal`, `Premium`, and `Good`: this is therefore a "categorical" and not a "continuous" variable.

We could also see that the data type indication for `cut` was `<ord>`, which is short for *ordered factor*. In R, categorical variables can be represented not just as character strings or integers, but also as **factors**. Factors have a defined set of *levels* which can be assigned a custom order. That is handy when plotting or when you need to set a reference level in a statistical model. (For more, see [the page for this previous Code Club session on factors](/codeclub/06_factors/).)

To quickly see which values the variable `cut` contains, and what their frequencies are, we can use [`count()`](https://dplyr.tidyverse.org/reference/count.html):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>diamonds</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='nf'><a href='https://dplyr.tidyverse.org/reference/count.html'>count</a></span><span class='o'>(</span><span class='nv'>cut</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 5 × 2</span></span></span>
<span><span class='c'>#&gt;   cut           n</span></span>
<span><span class='c'>#&gt;   <span style='color: #555555; font-style: italic;'>&lt;ord&gt;</span>     <span style='color: #555555; font-style: italic;'>&lt;int&gt;</span></span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>1</span> Fair       <span style='text-decoration: underline;'>1</span>610</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>2</span> Good       <span style='text-decoration: underline;'>4</span>906</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>3</span> Very Good <span style='text-decoration: underline;'>12</span>082</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>4</span> Premium   <span style='text-decoration: underline;'>13</span>791</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>5</span> Ideal     <span style='text-decoration: underline;'>21</span>551</span></span></code></pre>

</div>

To get a feel for the distribution of a categorical variable, making a barplot can also be useful. Recall that when making a plot with *ggplot2*, we at least need the following components:

-   The [`ggplot()`](https://ggplot2.tidyverse.org/reference/ggplot.html) function, in which we supply the dataframe that we want to use.

-   A *geom* function, which is basically the type of plot we want to make, such as [`geom_point()`](https://ggplot2.tidyverse.org/reference/geom_point.html) for a scatterplot and [`geom_bar()`](https://ggplot2.tidyverse.org/reference/geom_bar.html) for a barplot.

-   An "aesthetic mapping" that defines which variables to plot along the axes.

For a barplot showing `cut`, our *ggplot2* code would look as follows:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>data <span class='o'>=</span> <span class='nv'>diamonds</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_bar.html'>geom_bar</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>cut</span><span class='o'>)</span><span class='o'>)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-5-1.png" width="700px" style="display: block; margin: auto;" />

</div>

When making plots, we typically specify which variable should go along the y-axis, too. But that is not the case for barplots, where the default is to automatically plot a *count which is **computed** from the data*.

<br>

### Exploring variation in a continuous variable

We'll take another look at the `diamonds` dataframe and pick a *continuous* variable:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>diamonds</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 53,940 × 10</span></span></span>
<span><span class='c'>#&gt;    carat cut       color clarity depth table price     x     y     z</span></span>
<span><span class='c'>#&gt;    <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;ord&gt;</span>     <span style='color: #555555; font-style: italic;'>&lt;ord&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;ord&gt;</span>   <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;int&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span></span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 1</span>  0.23 Ideal     E     SI2      61.5    55   326  3.95  3.98  2.43</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 2</span>  0.21 Premium   E     SI1      59.8    61   326  3.89  3.84  2.31</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 3</span>  0.23 Good      E     VS1      56.9    65   327  4.05  4.07  2.31</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 4</span>  0.29 Premium   I     VS2      62.4    58   334  4.2   4.23  2.63</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 5</span>  0.31 Good      J     SI2      63.3    58   335  4.34  4.35  2.75</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 6</span>  0.24 Very Good J     VVS2     62.8    57   336  3.94  3.96  2.48</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 7</span>  0.24 Very Good I     VVS1     62.3    57   336  3.95  3.98  2.47</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 8</span>  0.26 Very Good H     SI1      61.9    55   337  4.07  4.11  2.53</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 9</span>  0.22 Fair      E     VS2      65.1    61   337  3.87  3.78  2.49</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>10</span>  0.23 Very Good H     VS1      59.4    61   338  4     4.05  2.39</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># … with 53,930 more rows</span></span></span></code></pre>

</div>

Let's explore the variation in the continuous variable `carat`, and do so by making a histogram using [`geom_histogram()`](https://ggplot2.tidyverse.org/reference/geom_histogram.html):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>data <span class='o'>=</span> <span class='nv'>diamonds</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>carat</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.</span></span></code></pre>
<img src="figs/unnamed-chunk-7-1.png" width="700px" style="display: block; margin: auto;" />

</div>

Under the hood, a histogram discretizes the continuous data into bins, and then shows the counts (here: number of diamonds) in each bin. We may want to play around with the width of the bins to see more fine-grained or coarse-grained patterns, and can do so using the `binwidth` argument:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>data <span class='o'>=</span> <span class='nv'>diamonds</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>carat</span><span class='o'>)</span>, binwidth <span class='o'>=</span> <span class='m'>0.5</span><span class='o'>)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-8-1.png" width="700px" style="display: block; margin: auto;" />

</div>

If we wanted to see this kind of representation in table-form, using [`count()`](https://dplyr.tidyverse.org/reference/count.html) directly wouldn't work: we don't have a column with bins for `carat`, only the raw, numeric values.

To create bins, we can use the *ggplot2* function `cut_width`, whose `width` argument is equivalent to `geom_histogram`'s `binwidth`:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>diamonds</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='nf'><a href='https://dplyr.tidyverse.org/reference/mutate.html'>mutate</a></span><span class='o'>(</span>carat_discrete <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/cut_interval.html'>cut_width</a></span><span class='o'>(</span><span class='nv'>carat</span>, width <span class='o'>=</span> <span class='m'>0.5</span><span class='o'>)</span><span class='o'>)</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='nf'><a href='https://dplyr.tidyverse.org/reference/count.html'>count</a></span><span class='o'>(</span><span class='nv'>carat_discrete</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 11 × 2</span></span></span>
<span><span class='c'>#&gt;    carat_discrete     n</span></span>
<span><span class='c'>#&gt;    <span style='color: #555555; font-style: italic;'>&lt;fct&gt;</span>          <span style='color: #555555; font-style: italic;'>&lt;int&gt;</span></span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 1</span> [-0.25,0.25]     785</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 2</span> (0.25,0.75]    <span style='text-decoration: underline;'>29</span>498</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 3</span> (0.75,1.25]    <span style='text-decoration: underline;'>15</span>977</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 4</span> (1.25,1.75]     <span style='text-decoration: underline;'>5</span>313</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 5</span> (1.75,2.25]     <span style='text-decoration: underline;'>2</span>002</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 6</span> (2.25,2.75]      322</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 7</span> (2.75,3.25]       32</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 8</span> (3.25,3.75]        5</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 9</span> (3.75,4.25]        4</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>10</span> (4.25,4.75]        1</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>11</span> (4.75,5.25]        1</span></span></code></pre>

</div>

### Multiple variables

If we want to show the variation in `carat` *separately for each level of `cut`*, we can map `carate` *also* to `fill`, which is the fill color of the bars:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'># First, let's subset the data to only keep relatively small diamonds: </span></span>
<span><span class='nv'>smaller</span> <span class='o'>&lt;-</span> <span class='nv'>diamonds</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='nf'><a href='https://dplyr.tidyverse.org/reference/filter.html'>filter</a></span><span class='o'>(</span><span class='nv'>carat</span> <span class='o'>&lt;</span> <span class='m'>3</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># Then, we make the plot:</span></span>
<span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>data <span class='o'>=</span> <span class='nv'>smaller</span>,</span>
<span>       mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>carat</span>, fill <span class='o'>=</span> <span class='nv'>cut</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>binwidth <span class='o'>=</span> <span class='m'>0.1</span>, color <span class='o'>=</span> <span class='s'>"grey20"</span><span class='o'>)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-10-1.png" width="700px" style="display: block; margin: auto;" />
<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='c'># Above, note that:</span></span>
<span><span class='c'># - The mapping is now inside 'ggplot()', and we used 'cut' twice</span></span>
<span><span class='c'># - In geom_histogram(), color is _not_ a mapping and is for the color of the border</span></span></code></pre>

</div>

Though in a case like this, a linegraph with [`geom_freqpoly()`](https://ggplot2.tidyverse.org/reference/geom_histogram.html) might be easier to interpret:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>data <span class='o'>=</span> <span class='nv'>smaller</span>,</span>
<span>       mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>carat</span>, colour <span class='o'>=</span> <span class='nv'>cut</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_freqpoly</a></span><span class='o'>(</span>binwidth <span class='o'>=</span> <span class='m'>0.1</span>, size <span class='o'>=</span> <span class='m'>1.5</span><span class='o'>)</span> <span class='c'># (Making thicker lines with 'size')</span></span>
</code></pre>
<img src="figs/unnamed-chunk-11-1.png" width="700px" style="display: block; margin: auto;" />

</div>

### Unusual values (outliers)

Sometimes, plots like histograms have very wide axis limits yet no visible bars on the sides:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span><span class='nv'>diamonds</span><span class='o'>)</span> <span class='o'>+</span> </span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>y</span><span class='o'>)</span>, binwidth <span class='o'>=</span> <span class='m'>0.5</span><span class='o'>)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-12-1.png" width="700px" style="display: block; margin: auto;" />

</div>

The x-axis limits are automatically picked based on the data, so there really *should* be some values all the way up to about 60. We just can't see them, since the y-axis scale goes all the way up to 12,000.

If we want to see these counts in the graph, we can *zoom in* on the y-axis with [`coord_cartesian()`](https://ggplot2.tidyverse.org/reference/coord_cartesian.html):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span><span class='nv'>diamonds</span><span class='o'>)</span> <span class='o'>+</span> </span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>y</span><span class='o'>)</span>, binwidth <span class='o'>=</span> <span class='m'>0.5</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/coord_cartesian.html'>coord_cartesian</a></span><span class='o'>(</span>ylim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='m'>50</span><span class='o'>)</span><span class='o'>)</span> <span class='c'># c(&lt;lower-limit&gt;, &lt;upper-limit&gt;)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-13-1.png" width="700px" style="display: block; margin: auto;" />

</div>

Note that in *ggplot2*, zooming in on a graph and setting axis limits isn't the same thing: you'll learn more about that in the exercises.

Of course we could also try to find these values in the dataframe itself, which might be more useful than a graph in cases like this. To do so, we can use the [`filter()`](https://dplyr.tidyverse.org/reference/filter.html) function we learned about in the previous chapter:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>diamonds</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> <span class='nf'><a href='https://dplyr.tidyverse.org/reference/filter.html'>filter</a></span><span class='o'>(</span><span class='nv'>y</span> <span class='o'>&lt;</span> <span class='m'>3</span> <span class='o'>|</span> <span class='nv'>y</span> <span class='o'>&gt;</span> <span class='m'>20</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 9 × 10</span></span></span>
<span><span class='c'>#&gt;   carat cut       color clarity depth table price     x     y     z</span></span>
<span><span class='c'>#&gt;   <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;ord&gt;</span>     <span style='color: #555555; font-style: italic;'>&lt;ord&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;ord&gt;</span>   <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;int&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span></span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>1</span>  1    Very Good H     VS2      63.3    53  <span style='text-decoration: underline;'>5</span>139  0      0    0   </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>2</span>  1.14 Fair      G     VS1      57.5    67  <span style='text-decoration: underline;'>6</span>381  0      0    0   </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>3</span>  2    Premium   H     SI2      58.9    57 <span style='text-decoration: underline;'>12</span>210  8.09  58.9  8.06</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>4</span>  1.56 Ideal     G     VS2      62.2    54 <span style='text-decoration: underline;'>12</span>800  0      0    0   </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>5</span>  1.2  Premium   D     VVS1     62.1    59 <span style='text-decoration: underline;'>15</span>686  0      0    0   </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>6</span>  2.25 Premium   H     SI2      62.8    59 <span style='text-decoration: underline;'>18</span>034  0      0    0   </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>7</span>  0.51 Ideal     E     VS1      61.8    55  <span style='text-decoration: underline;'>2</span>075  5.15  31.8  5.12</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>8</span>  0.71 Good      F     SI2      64.1    60  <span style='text-decoration: underline;'>2</span>130  0      0    0   </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>9</span>  0.71 Good      F     SI2      64.1    60  <span style='text-decoration: underline;'>2</span>130  0      0    0</span></span></code></pre>

</div>

<br>

------------------------------------------------------------------------

## Breakout Rooms

These exercises will continue to use the `diamonds` data, which is automatically loaded when you load the *tidyverse*.

<div class="puzzle">

<div>

### Exercise 1

In the `diamonds` data, explore the distribution of `price`, which is the price of a diamond in USD. Do you discover anything unusual or surprising?

Make sure to try different values for the `binwidth` argument!

<details>
<summary>
<b>Hints</b> (click here)
</summary>

<br>

-   This is a continuous variable, so use [`geom_histogram()`](https://ggplot2.tidyverse.org/reference/geom_histogram.html).

-   A more fine-grained plot (smaller bins with `binwidth`) than the default should reveal something odd.

-   You might want to use [`coord_cartesian()`](https://ggplot2.tidyverse.org/reference/coord_cartesian.html) to see the area with the odd pattern in more detail. (Alternatively, you could try [`filter()`](https://dplyr.tidyverse.org/reference/filter.html)ing the data before plotting.)

</details>

<br>

<details>
<summary>
<b>Solution</b> (click here)
</summary>

<br>

-   [`geom_histogram()`](https://ggplot2.tidyverse.org/reference/geom_histogram.html) with default settings doesn't reveal anything too weird:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>data <span class='o'>=</span> <span class='nv'>diamonds</span>,</span>
<span>       mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>price</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.</span></span></code></pre>
<img src="figs/unnamed-chunk-15-1.png" width="700px" style="display: block; margin: auto;" />

</div>

-   But with a binwidth of e.g. 100, we start to see something odd: a gap in the distribution.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>data <span class='o'>=</span> <span class='nv'>diamonds</span>,</span>
<span>       mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>price</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>binwidth <span class='o'>=</span> <span class='m'>100</span><span class='o'>)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-16-1.png" width="700px" style="display: block; margin: auto;" />

</div>

-   Let's take a closer look by zooming in on prices of \$2,500 or less:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>diamonds</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>price</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>binwidth <span class='o'>=</span> <span class='m'>25</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/coord_cartesian.html'>coord_cartesian</a></span><span class='o'>(</span>xlim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='m'>2500</span><span class='o'>)</span><span class='o'>)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-17-1.png" width="700px" style="display: block; margin: auto;" />

</div>

(An alternative approach would be to filter the data before plotting:)

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>diamonds</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='nf'><a href='https://dplyr.tidyverse.org/reference/filter.html'>filter</a></span><span class='o'>(</span><span class='nv'>price</span> <span class='o'>&lt;</span> <span class='m'>2500</span><span class='o'>)</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>price</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>binwidth <span class='o'>=</span> <span class='m'>25</span><span class='o'>)</span></span></code></pre>

</div>

I have no idea why there are no diamonds with a price of around \$1,500 -- anybody?

</details>

</div>

</div>

<br>

<div class="puzzle">

<div>

### Exercise 2

Compare [`coord_cartesian()`](https://ggplot2.tidyverse.org/reference/coord_cartesian.html) with the similar function [`lims()`](https://ggplot2.tidyverse.org/reference/lims.html) to see a narrower range along the y-axis for a histogram. Specifically, make two histograms of `price` with a y-axis that only goes up to 3,000: one with `coord_cartesian(ylim = ...)` and one with `lims(y = ...)`.

What is happening in the graph made with [`lims()`](https://ggplot2.tidyverse.org/reference/lims.html)?

(See the hint for example usage of [`lims()`](https://ggplot2.tidyverse.org/reference/lims.html), a function we haven't seen yet.)

<details>
<summary>
<b>Hints</b> (click here)
</summary>

<br>

You can use [`lims()`](https://ggplot2.tidyverse.org/reference/lims.html) to set arbitrary axis limits:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span><span class='nv'>diamonds</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_point.html'>geom_point</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>x</span>, y <span class='o'>=</span> <span class='nv'>y</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/lims.html'>lims</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>5</span>, <span class='m'>10</span><span class='o'>)</span>,   <span class='c'># c(&lt;lower-limit&gt;, &lt;upper-limit&gt;)</span></span>
<span>       y <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='m'>20</span><span class='o'>)</span><span class='o'>)</span>   <span class='c'># c(&lt;lower-limit&gt;, &lt;upper-limit&gt;)</span></span>
<span><span class='c'>#&gt; Warning: Removed 17593 rows containing missing values (geom_point).</span></span></code></pre>
<img src="figs/unnamed-chunk-19-1.png" width="700px" style="display: block; margin: auto;" />

</div>

You could also use the very similar [`ylim()`](https://ggplot2.tidyverse.org/reference/lims.html) / [`xlim()`](https://ggplot2.tidyverse.org/reference/lims.html) pair of functions, though note the slightly simplified syntax:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span><span class='nv'>diamonds</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_point.html'>geom_point</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>x</span>, y <span class='o'>=</span> <span class='nv'>y</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/lims.html'>xlim</a></span><span class='o'>(</span><span class='m'>5</span>, <span class='m'>10</span><span class='o'>)</span> <span class='o'>+</span>   <span class='c'># Note: you don't pass a vector inside 'c()' here</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/lims.html'>ylim</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='m'>20</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; Warning: Removed 17593 rows containing missing values (geom_point).</span></span></code></pre>
<img src="figs/unnamed-chunk-20-1.png" width="700px" style="display: block; margin: auto;" />

</div>

</details>

<br>

<details>
<summary>
<b>Solution</b> (click here)
</summary>

<br>

Whereas the graph produced with [`coord_cartesian()`](https://ggplot2.tidyverse.org/reference/coord_cartesian.html) is simply "cut off" at the specified limit, the graph produced with [`lims()`](https://ggplot2.tidyverse.org/reference/lims.html) is missing bars!

It turns out that *ggplot2* removes the bars that can't be shown given our y-limit. Notice that it warns us about doing so: `#> Warning: Removed 5 rows containing missing values (geom_bar).`

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span><span class='nv'>diamonds</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>price</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/coord_cartesian.html'>coord_cartesian</a></span><span class='o'>(</span>ylim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='m'>3000</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.</span></span></code></pre>
<img src="figs/unnamed-chunk-21-1.png" width="700px" style="display: block; margin: auto;" />

</div>

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span><span class='nv'>diamonds</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>price</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/lims.html'>lims</a></span><span class='o'>(</span>y <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='m'>3000</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.</span></span><span><span class='c'>#&gt; Warning: Removed 5 rows containing missing values (geom_bar).</span></span></code></pre>
<img src="figs/unnamed-chunk-22-1.png" width="700px" style="display: block; margin: auto;" />

</div>

</details>

</div>

</div>

<br>

<div class="puzzle">

<div>

### Exercise 3

Using scatterplots, explore the relationship between the width `y` and the depth `z` of the diamonds.

What do you think about the outliers? Are they more likely to be unusual diamonds or data entry errors?

<details>
<summary>
<b>Hints</b> (click here)
</summary>

<br>

-   Make a scatterplot with [`geom_point()`](https://ggplot2.tidyverse.org/reference/geom_point.html).

-   Zoom in on the area with most points, to get a better feel for the overall relationship between `y` and `z`.

-   Could a diamond with a value of `y` larger than 20 just be a very large diamond? Or does the corresponding value for `z`, and the overall relationship between `y` and `z` make it more likely that they are outliers?

</details>

<br>

<details>
<summary>
<b>Solution</b> (click here)
</summary>

<br>

Let's start with a simple scatterplot with all data and default axis limits:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>data <span class='o'>=</span> <span class='nv'>diamonds</span>,</span>
<span>       mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>z</span>, y <span class='o'>=</span> <span class='nv'>y</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_point.html'>geom_point</a></span><span class='o'>(</span><span class='o'>)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-23-1.png" width="700px" style="display: block; margin: auto;" />

</div>

Phew! There are definitely some striking outliers. Let's zoom in on the main cloud of points:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>data <span class='o'>=</span> <span class='nv'>diamonds</span>,</span>
<span>       mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>z</span>, y <span class='o'>=</span> <span class='nv'>y</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_point.html'>geom_point</a></span><span class='o'>(</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/coord_cartesian.html'>coord_cartesian</a></span><span class='o'>(</span>xlim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='m'>10</span><span class='o'>)</span>, ylim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='m'>15</span><span class='o'>)</span><span class='o'>)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-24-1.png" width="700px" style="display: block; margin: auto;" />

</div>

That looks like an overall very tight correlation between width (`y`) and depth (`z`).

Therefore, the outliers of `y` and `z` don't just seem to represent very large or very small diamonds, and are likely data entry errors or something along those lines.

</details>

</div>

</div>

<br>

<div class="puzzle">

<div>

### Exercise 4 (bonus)

Explore the distribution of `carat`. Specifically, compare the number of diamonds of 0.99 (and a little less) carat and those of 1 (and a little more) carat? What do you think is the cause of the difference?

<details>
<summary>
<b>Hints</b> (click here)
</summary>

<br>

-   Make a histogram ([`geom_histogram()`](https://ggplot2.tidyverse.org/reference/geom_histogram.html)) for `carat`, and optionally zoom in to the area around 1.

-   Use [`filter()`](https://dplyr.tidyverse.org/reference/filter.html) and [`count()`](https://dplyr.tidyverse.org/reference/count.html) to specifically check out the diamond counts with a carat of around 1.

</details>

<br>

<details>
<summary>
<b>Solution</b> (click here)
</summary>

<br>

We can start by simply making a histogram for `carat`:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>data <span class='o'>=</span> <span class='nv'>diamonds</span>,</span>
<span>       mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>carat</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>binwidth <span class='o'>=</span> <span class='m'>0.01</span><span class='o'>)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-25-1.png" width="700px" style="display: block; margin: auto;" />

</div>

That's a weird pattern, with a bunch of peaks and valleys! Let's just show the area around a carat of `1`:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>diamonds</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='nf'><a href='https://dplyr.tidyverse.org/reference/filter.html'>filter</a></span><span class='o'>(</span><span class='nv'>carat</span> <span class='o'>&gt;</span> <span class='m'>0.9</span>, <span class='nv'>carat</span> <span class='o'>&lt;</span> <span class='m'>1.1</span><span class='o'>)</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='o'>(</span>mapping <span class='o'>=</span> <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='o'>(</span>x <span class='o'>=</span> <span class='nv'>carat</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>+</span></span>
<span>  <span class='nf'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='o'>(</span>binwidth <span class='o'>=</span> <span class='m'>0.01</span><span class='o'>)</span></span>
</code></pre>
<img src="figs/unnamed-chunk-26-1.png" width="700px" style="display: block; margin: auto;" />

</div>

There's clearly a big uptick around `1`, but checking out the raw counts would make it easier to answer the original question:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>diamonds</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='nf'><a href='https://dplyr.tidyverse.org/reference/filter.html'>filter</a></span><span class='o'>(</span><span class='nv'>carat</span> <span class='o'>&gt;</span> <span class='m'>0.9</span>, <span class='nv'>carat</span> <span class='o'>&lt;</span> <span class='m'>1.1</span><span class='o'>)</span> <span class='o'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='nf'><a href='https://dplyr.tidyverse.org/reference/count.html'>count</a></span><span class='o'>(</span><span class='nv'>carat</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 19 × 2</span></span></span>
<span><span class='c'>#&gt;    carat     n</span></span>
<span><span class='c'>#&gt;    <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;int&gt;</span></span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 1</span>  0.91   570</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 2</span>  0.92   226</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 3</span>  0.93   142</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 4</span>  0.94    59</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 5</span>  0.95    65</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 6</span>  0.96   103</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 7</span>  0.97    59</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 8</span>  0.98    31</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'> 9</span>  0.99    23</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>10</span>  1     <span style='text-decoration: underline;'>1</span>558</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>11</span>  1.01  <span style='text-decoration: underline;'>2</span>242</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>12</span>  1.02   883</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>13</span>  1.03   523</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>14</span>  1.04   475</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>15</span>  1.05   361</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>16</span>  1.06   373</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>17</span>  1.07   342</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>18</span>  1.08   246</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>19</span>  1.09   287</span></span></code></pre>

</div>

There are suspiciously few diamonds with a carat of 0.99 (and, to a lesser extent, with a carat anywhere above 0.9): could there be some rounding-up going on?

</details>

</div>

</div>

<br>

