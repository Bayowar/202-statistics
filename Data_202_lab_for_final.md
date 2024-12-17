Data_202_labreport_for_final
================
Bayowa Onabajo
2024-11-28

# Introduction

The goal of this visualization is to show how often specific themes
(such as Chronic Stress, Misdiagnosis, Social Isolation, Generational
Trauma, Discrimination) appear in the AI responses over time. This is
important to help demonstrate which issues are consistently identified
as impactful for Black women’s mental health by the A.I system.

# Research question and prompt for A.I

“How does algorithmic bias in health systems impact the mental health of
Black women in the U.S.?” and follow up was “Is the impact significant
and why?” Prompts were submitted weekly for three months and responses
were collated and analyzed below.

# A.I reaponse collection dataframe

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
# Create a data frame
coding_table <- data.frame(
  Date = c("Sep. 16", "Sep. 23", "Oct. 7", "Oct. 14", "Oct. 21", "Oct. 28"),
  AI_Response = c(
    "Chronic stress, misdiagnosis",
    "Social isolation, intergenerational trauma",
    "Anxiety, Chronic stress",
    "Anxiety, Chronic stress",
    "Chronic stress, misdiagnosis",
    "Anxiety, Chronic stress"
  ),
  Theme = c(
    "Mental Health Impact", "Generational Trauma", "Mental Health Impact",
    "Mental Health Impact", "Mental Health Impact", "Mental Health Impact"
  ),
  Key_Term = c(
    "Chronic Stress", "Social Isolation", "Anxiety",
    "Anxiety", "Chronic Stress", "Anxiety"
  )
)

# View the data frame
print(coding_table)
```

    ##      Date                                AI_Response                Theme
    ## 1 Sep. 16               Chronic stress, misdiagnosis Mental Health Impact
    ## 2 Sep. 23 Social isolation, intergenerational trauma  Generational Trauma
    ## 3  Oct. 7                    Anxiety, Chronic stress Mental Health Impact
    ## 4 Oct. 14                    Anxiety, Chronic stress Mental Health Impact
    ## 5 Oct. 21               Chronic stress, misdiagnosis Mental Health Impact
    ## 6 Oct. 28                    Anxiety, Chronic stress Mental Health Impact
    ##           Key_Term
    ## 1   Chronic Stress
    ## 2 Social Isolation
    ## 3          Anxiety
    ## 4          Anxiety
    ## 5   Chronic Stress
    ## 6          Anxiety

``` r
library(knitr)

# Generate a format table
kable(coding_table, format = "markdown", caption = "Response Table")
```

| Date | AI_Response | Theme | Key_Term |
|:---|:---|:---|:---|
| Sep. 16 | Chronic stress, misdiagnosis | Mental Health Impact | Chronic Stress |
| Sep. 23 | Social isolation, intergenerational trauma | Generational Trauma | Social Isolation |
| Oct. 7 | Anxiety, Chronic stress | Mental Health Impact | Anxiety |
| Oct. 14 | Anxiety, Chronic stress | Mental Health Impact | Anxiety |
| Oct. 21 | Chronic stress, misdiagnosis | Mental Health Impact | Chronic Stress |
| Oct. 28 | Anxiety, Chronic stress | Mental Health Impact | Anxiety |

Response Table

# Bar chart creation

# Plot Bar Chart using ggplot

``` r
library(ggplot2)  # Make sure ggplot2 is installed

ggplot(theme_count, aes(x=Theme, y=Count, fill=Theme)) +
  geom_bar(stat="identity") +  # 'identity' is used because the count is already provided
  labs(title="Frequency of Themes in AI Responses", x="Theme", y="Count") +
  theme_minimal()  # A clean theme for the plot
```

![](Data_202_lab_for_final_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

The Bar chart allows for a straightforward comparison of how frequently
certain issues (like misdiagnosis or chronic stress) appear in
AI-generated responses. This visualization makes it easier to see which
problems are identified as the most significant concerning Black women’s
mental health in AI systems/Algorithms.

# Word cloud utilization

The word cloud provides a visualization of the frequent terms used in
the AI responses. Terms like stress, misdiagnosis, depression, and
health disparities may appear more frequently than others. A word cloud
visually emphasizes the terms that were mentioned most often, giving a
quick sense of which concepts are central to the AI’s understanding of
the problem.

# Word cloud

``` r
#load packages
library(wordcloud)
```

    ## Loading required package: RColorBrewer

``` r
library(RColorBrewer)
library(tm)
```

    ## Loading required package: NLP

    ## 
    ## Attaching package: 'NLP'

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     annotate

``` r
#collate terms from A.I response here
text <- c("misdiagnosis", "chronic stress", "health disparities", "social isolation", "anxiety", "Generational Trauma", "Discrimination", "mistrust")


wordcloud(text, scale=c(1.5,0.5), max.words=10, colors=brewer.pal(8, "Dark2"))
```

    ## Warning in tm_map.SimpleCorpus(corpus, tm::removePunctuation): transformation
    ## drops documents

    ## Warning in tm_map.SimpleCorpus(corpus, function(x) tm::removeWords(x,
    ## tm::stopwords())): transformation drops documents

![](Data_202_lab_for_final_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

A word cloud is a visually appealing way to identify the most common
terms across multiple responses and is useful for summarizing the key
concepts that the AI recognized as significant impacts on Black women’s
mental health, providing a visual impression of the focus of my
analysis.

# Results

The bar chart findings alligns with some already established articles on
this subject with an increase frequency of anxiety, chronic stress
amongst black women in a.i responses over my period of testing. In
addition, based on the responses visualized in the barchart i can infer
that there may be significant impact and is significant due to some of
the high frequency of responses in some conditions (e.g. anxiety) which
answers the prompt given to the a.i system in my initial research phase.
This also alligns with the referenced literature in my paper on how
algorithmic biases have an impact on the mental health of black women
and marginalized groups with a potential for creating more disparity if
not addressed. word cloud visualization supplements my analysis by
showing the responses that are most frequent in a.i response

# Limitations

A.I responses failed to capture historical biases in all responses
captured which could mean a.i systems are not really trained on
historical data which affected black communities.

# Conclusion

This analysis helps to relate the findings to existing literature on
algorithmic bias in healthcare. By interpreting these visualizations,
its easier to draw deeper insights into how algorithmic bias affects the
mental health of Black women, ultimately supporting this research.
