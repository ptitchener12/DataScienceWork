RMS Titanic
================
(Your name here)
2020-

  - [Grading Rubric](#grading-rubric)
      - [Individual](#individual)
      - [Team](#team)
      - [Due Date](#due-date)
  - [First Look](#first-look)
  - [Deeper Look](#deeper-look)
  - [Notes](#notes)

*Purpose*: Most datasets have at least a few variables. Part of our task
in analyzing a dataset is to understand trends as they vary across these
different variables. Unless we’re careful and thorough, we can easily
miss these patterns. In this challenge you’ll analyze a dataset with a
small number of categorical variables and try to find differences among
the groups.

*Reading*: (Optional) [Wikipedia
article](https://en.wikipedia.org/wiki/RMS_Titanic) on the RMS Titanic.

<!-- include-rubric -->

# Grading Rubric

<!-- -------------------------------------------------- -->

Unlike exercises, **challenges will be graded**. The following rubrics
define how you will be graded, both on an individual and team basis.

## Individual

<!-- ------------------------- -->

| Category    | Unsatisfactory                                                                   | Satisfactory                                                               |
| ----------- | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| Effort      | Some task **q**’s left unattempted                                               | All task **q**’s attempted                                                 |
| Observed    | Did not document observations                                                    | Documented observations based on analysis                                  |
| Supported   | Some observations not supported by analysis                                      | All observations supported by analysis (table, graph, etc.)                |
| Code Styled | Violations of the [style guide](https://style.tidyverse.org/) hinder readability | Code sufficiently close to the [style guide](https://style.tidyverse.org/) |

## Team

<!-- ------------------------- -->

| Category   | Unsatisfactory                                                                                   | Satisfactory                                       |
| ---------- | ------------------------------------------------------------------------------------------------ | -------------------------------------------------- |
| Documented | No team contributions to Wiki                                                                    | Team contributed to Wiki                           |
| Referenced | No team references in Wiki                                                                       | At least one reference in Wiki to member report(s) |
| Relevant   | References unrelated to assertion, or difficult to find related analysis based on reference text | Reference text clearly points to relevant analysis |

## Due Date

<!-- ------------------------- -->

All the deliverables stated in the rubrics above are due on the day of
the class discussion of that exercise. See the
[Syllabus](https://docs.google.com/document/d/1jJTh2DH8nVJd2eyMMoyNGroReo0BKcJrz1eONi3rPSc/edit?usp=sharing)
for more information.

``` r
library(tidyverse)
```

    ## -- Attaching packages -------------------------------------------------------------------------------------------------------------------------------------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.2     v purrr   0.3.4
    ## v tibble  3.0.1     v dplyr   1.0.0
    ## v tidyr   1.1.0     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## -- Conflicts ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
df_titanic <- as_tibble(Titanic)
```

*Background*: The RMS Titanic sank on its maiden voyage in 1912; about
67% of its passengers died.

# First Look

<!-- -------------------------------------------------- -->

**q1** Perform a glimpse of `df_titanic`. What variables are in this
dataset?

``` r
## TASK: Perform a `glimpse` of df_titanic
glimpse(df_titanic)
```

    ## Rows: 32
    ## Columns: 5
    ## $ Class    <chr> "1st", "2nd", "3rd", "Crew", "1st", "2nd", "3rd", "Crew", ...
    ## $ Sex      <chr> "Male", "Male", "Male", "Male", "Female", "Female", "Femal...
    ## $ Age      <chr> "Child", "Child", "Child", "Child", "Child", "Child", "Chi...
    ## $ Survived <chr> "No", "No", "No", "No", "No", "No", "No", "No", "No", "No"...
    ## $ n        <dbl> 0, 0, 35, 0, 0, 0, 17, 0, 118, 154, 387, 670, 4, 13, 89, 3...

**Observations**:

Class, Sex, Age, Survived, n - N seems to be the number of people with
similar previous characteristics.

**q2** Skim the [Wikipedia
article](https://en.wikipedia.org/wiki/RMS_Titanic) on the RMS Titanic,
and look for a total count of passengers. Compare against the total
computed below. Are there any differences? Are those differences large
or small? What might account for those differences?

2224 passengers + crew \~1500 died

``` r
## NOTE: No need to edit! We'll cover how to
## do this calculation in a later exercise.
df_titanic %>% summarize(total = sum(n))
```

    ## # A tibble: 1 x 1
    ##   total
    ##   <dbl>
    ## 1  2201

**Observations**:

The difference is 23 people. Poor historical data may account for this
difference. This data set may also have come out before new information
was found about the titanic passenger and crew list. Also there may have
been stowaways that boarded the ship without a ticket and therefore
would not have been counted as a passenger or crew.

From the wikipedia article: “confusion over the passenger list, which
included some names of people who canceled their trip at the last
minute, and the fact that several passengers traveled under aliases for
various reasons”

**q3** Create a plot showing the count of passengers who *did* survive,
along with aesthetics for `Class` and `Sex`. Document your observations
below.

*Note*: There are many ways to do this.

``` r
df_titanic %>% 
  filter(Survived == "Yes") %>% 
  group_by(Class,Sex) %>% 
  summarize(totalSex = sum(n)) %>% 
  ggplot() + 
  geom_point(aes(Class, totalSex, color = Sex), size = 3)
```

    ## `summarise()` regrouping output by 'Class' (override with `.groups` argument)

![](c01-titanic-assignment-PaulT_files/figure-gfm/q3-task-1.png)<!-- -->

**Observations**:

This plot is not that useful because it does not show percentages. For
example, there were likely none of the crew were children, and therefore
“none” of them survived. Also, it appears that only a small number of
children in first class survived, although in reality almost none of
them died, there were just very few of them. Likewise, a large number of
male crew survived, but that was because there were so many male crew on
board.

# Deeper Look

<!-- -------------------------------------------------- -->

Raw counts give us a sense of totals, but they are not as useful for
understanding differences between groups. This is because the
differences we see in counts could be due to either the relative size of
the group OR differences in outcomes for those groups. To make
comparisons between groups, we should also consider *proportions*.\[1\]

The following code computes proportions within each `Class, Sex, Age`
group.

``` r
## NOTE: No need to edit! We'll cover how to
## do this calculation in a later exercise.
df_prop <-
  df_titanic %>%
  group_by(Class, Sex, Age) %>%
  mutate(
    Total = sum(n),
    Prop = n / Total
  ) %>%
  ungroup()
df_prop
```

    ## # A tibble: 32 x 7
    ##    Class Sex    Age   Survived     n Total    Prop
    ##    <chr> <chr>  <chr> <chr>    <dbl> <dbl>   <dbl>
    ##  1 1st   Male   Child No           0     5   0    
    ##  2 2nd   Male   Child No           0    11   0    
    ##  3 3rd   Male   Child No          35    48   0.729
    ##  4 Crew  Male   Child No           0     0 NaN    
    ##  5 1st   Female Child No           0     1   0    
    ##  6 2nd   Female Child No           0    13   0    
    ##  7 3rd   Female Child No          17    31   0.548
    ##  8 Crew  Female Child No           0     0 NaN    
    ##  9 1st   Male   Adult No         118   175   0.674
    ## 10 2nd   Male   Adult No         154   168   0.917
    ## # ... with 22 more rows

**q4** Replicate your visual from q3, but display `Prop` in place of
`n`. Document your observations, and note any new/different observations
you make in comparison with q3.

``` r
df_titanic %>% 
  #filter(Survived == "Yes") %>% 
  group_by(Class,Sex,Survived) %>% 
  summarize(totalSex = sum(n)) %>% 
  #ungroup() %>% 
  mutate(total = sum(totalSex)) %>% 
  mutate(propSex = totalSex/total) %>%
  filter(Survived == "Yes") %>% 
  ggplot() + 
  geom_point(aes(Class, propSex, color = Sex),size = 3)
```

    ## `summarise()` regrouping output by 'Class', 'Sex' (override with `.groups` argument)

![](c01-titanic-assignment-PaulT_files/figure-gfm/q4-task-1.png)<!-- -->

**Observations**:

We can now see that women survived at a much higher rate than men. Third
class also generally survived at much lower rates than the other
passengers.

**q5** Create a plot showing the group-proportion of passengers who
*did* survive, along with aesthetics for `Class`, `Sex`, *and* `Age`.
Document your observations below.

*Hint*: Don’t forget that you can use `facet_grid` to help consider
additional variables\!

``` r
df_prop %>% 
  filter(Survived == "Yes") %>% 
  ggplot(aes(Class, Prop, fillnn = Age)) + 
  geom_bar(stat =  "identity") + 
  facet_grid(cols = vars(Sex), rows = vars(Age))
```

    ## Warning: Removed 2 rows containing missing values (position_stack).

![](c01-titanic-assignment-PaulT_files/figure-gfm/q5-task-1.png)<!-- -->

**Observations**:

First and second class children were by far the most likely to survive,
with a 100% survival rate (shown in this data set, this is actually
wrong historically). It is clear that “women and children first” was not
applied to third class children, who died at a very similar rate to the
rest of third class. Seconc class men were the least likely to survive.

# Notes

<!-- -------------------------------------------------- -->

\[1\] This is basically the same idea as [Dimensional
Analysis](https://en.wikipedia.org/wiki/Dimensional_analysis); computing
proportions is akin to non-dimensionalizing a quantity.
