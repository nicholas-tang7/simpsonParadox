# Understanding Simpson’s Paradox


## Introduction to Simpson’s Paradox

**Simpson’s Paradox** is one of the most fascinating and dangerous
phenomena in data analysis. It occurs when a trend appears in different
groups of data but disappears or reverses when these groups are
combined. This paradox can lead to completely wrong conclusions if we
don’t look beyond surface-level statistics.

The classic example comes from UC Berkeley’s 1973 graduate admissions
data, where women appeared to be discriminated against in the overall
admission rates, but when examined by department, women actually had
equal or higher admission rates than men.

In this tutorial, we’ll explore Simpson’s Paradox using the beloved
Palmer Station penguin dataset, examining the relationship between bill
length and bill depth across different penguin species.

``` r
# Set CRAN mirror for package installation
options(repos = c(CRAN = "https://cran.rstudio.com/"))

# Install required packages if not already installed
if (!require(patchwork, quietly = TRUE)) {
  install.packages("patchwork")
  library(patchwork)
}

library(ggplot2)
library(palmerpenguins)
data(penguins)
```

## The Data Story: Penguin Bill Dimensions

Let’s start by examining our data and building up to the paradox step by
step.

### A. The Overall Relationship

``` r
plot1 <- ggplot(data = penguins,
        aes(x = bill_length_mm,
        y = bill_depth_mm)) +
  theme_minimal(16) +
  geom_point(alpha = 0.6, color = "steelblue") +
  labs(title = "Overall Relationship",
       subtitle = "Bill Length vs. Bill Depth",
       x = "Bill length (mm)",
       y = "Bill depth (mm)") +
  theme(plot.title.position = "plot",
        plot.caption = element_text(hjust = 0, face= "italic"),
        plot.caption.position = "plot",
        plot.title = element_text(size = 14, margin = margin(b = 20)),
        plot.subtitle = element_text(size = 12, margin = margin(b = 15))) +
  geom_smooth(method = "lm", se = FALSE, color = "red", linewidth = 1.5)

plot1
```

![](README_files/figure-commonmark/unnamed-chunk-1-1.png)

At first glance, we see a **positive relationship** between bill length
and bill depth. The red trendline suggests that longer bills tend to be
deeper. This seems straightforward, but let’s dig deeper…

### B. The Hidden Truth: Species-Specific Relationships

Now let’s color our points by species and see what happens when we
examine the relationship within each group:

``` r
plot2 <- ggplot(data = penguins,
       aes(x = bill_length_mm,
           y = bill_depth_mm,
           color = species)) +
  theme_minimal(16) +
  geom_point(alpha = 0.7) +
  scale_color_manual(values = c("darkorange","purple","cyan4"),
                     name = "Species") +
  labs(title = "Simpson's Paradox Revealed!",
       subtitle = "Species-specific relationships",
       x = "Bill length (mm)",
       y = "Bill depth (mm)",
       caption = "Red line: Overall trend | Colored lines: Within-species trends") +
  theme(plot.title.position = "plot",
        plot.caption = element_text(hjust = 0, face = "italic"),
        plot.caption.position = "plot",
        legend.position = "bottom",
        plot.title = element_text(size = 14, margin = margin(b = 20)),
        plot.subtitle = element_text(size = 12, margin = margin(b = 15))) +
  # Overall trend (ignoring species)
  geom_smooth(method = "lm", se = FALSE, color = "red", 
              linewidth = 1.5, alpha = 0.8) +
  # Species-specific trends
  geom_smooth(method = "lm", se = FALSE, linewidth = 1)

plot2
```

![Simpson’s Paradox Revealed: Overall vs. Species-Specific
Relationships](README_files/figure-commonmark/unnamed-chunk-2-1.png)

**The Paradox Revealed!** 🎯

Notice what just happened: - **Overall trend (red line):** Positive
relationship - longer bills are deeper - **Within each species (colored
lines):** Negative relationship - longer bills are actually shallower!

This is Simpson’s Paradox in action. The species variable is a
**confounding variable** that completely reverses the apparent
relationship when we aggregate the data.

### C. Side-by-Side Comparison

Now let’s create a compelling side-by-side comparison that makes
Simpson’s Paradox crystal clear:

``` r
# Create the side-by-side comparison using patchwork
comparison_plot <- plot1 + plot2 +
  plot_layout(guides = "collect", widths = c(1, 1), ncol = 2) +
  plot_annotation(
    title = "Simpson's Paradox: The Same Data, Two Different Stories",
    subtitle = "Left: Overall relationship (misleading) | Right: Species-specific relationships (truth)",
    caption = "The paradox: A positive overall trend masks negative within-species trends due to species confounding",
    theme = theme_minimal(16) +
      theme(
        plot.title = element_text(size = 18, face = "bold", hjust = 0.5, margin = margin(b = 40)),
        plot.subtitle = element_text(size = 14, hjust = 0.5, color = "darkred", margin = margin(b = 30)),
        plot.caption = element_text(size = 12, hjust = 0, face = "italic", color = "darkblue", margin = margin(t = 30)),
        plot.title.position = "plot",
        plot.caption.position = "plot"
      )
  )

comparison_plot
```

<img src="README_files/figure-commonmark/unnamed-chunk-3-1.png"
style="width:100.0%"
alt="Side-by-Side Comparison: Simpson’s Paradox in Action" />

**The Power of Side-by-Side Comparison!** 🎯

This visualization perfectly demonstrates Simpson’s Paradox: - **Left
plot:** Shows the misleading overall positive relationship (red line
going up) - **Right plot:** Reveals the truth - within each species, the
relationship is actually negative (colored lines going down)

The contrast is striking and immediately shows why we must always
consider confounding variables and group-specific relationships before
drawing conclusions from aggregate data.

## Key Takeaways: Lessons from Simpson’s Paradox

### The Power of Visualization

- **Always visualize your data** before drawing conclusions from
  statistical models
- A single trendline can be dangerously misleading when it masks
  underlying group differences
- **P-values are not the answer** - statistical significance doesn’t
  guarantee the relationship is meaningful or correctly interpreted

### Simpson’s Paradox Dangers

- **Aggregated data can reverse relationships** - what appears to be a
  positive correlation overall might be negative within each group
- **Business implications:** Making decisions based on aggregate trends
  without considering subgroups can lead to costly mistakes
- **The “lurking variable” problem:** Always ask “What am I missing?”
  when relationships seem counterintuitive

### Real-World Examples

- **UC Berkeley admissions (1973):** Overall admission rates suggested
  gender bias against women, but within each department, women had equal
  or higher admission rates
- **Baseball batting averages:** A player can have a higher batting
  average than another in both halves of a season, yet a lower overall
  average
- **Medical studies:** A treatment can appear harmful overall but
  beneficial within each age group
- **Marketing campaigns:** A campaign might seem ineffective overall but
  highly successful within specific customer segments

### Best Practices for Data Analysis

- **Stratify your analysis** by relevant grouping variables
  (demographics, categories, time periods)
- **Look for confounding variables** that might explain apparent
  relationships
- **Use multiple visualization approaches** (overall vs. faceted plots,
  side-by-side comparisons)
- **Question your assumptions** - if a relationship seems too good (or
  bad) to be true, it might be!

## Conclusion

Simpson’s Paradox teaches us that **context matters**. The penguin data
shows us that what appears to be a simple positive relationship between
bill length and depth is actually masking a more complex reality where
the relationship is negative within each species. This paradox reminds
us to always dig deeper, consider confounding variables, and never trust
aggregate statistics without examining the underlying groups.

Remember: **The truth is in the details, not in the summary
statistics.**
