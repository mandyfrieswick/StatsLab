# Digital Lab Part 1: The Pit Viper Umwelt Test
**Author:** [MANDY FRIESWICK]  
**Date:** [19 MARCH 2026]  

## The Scenario: You are the P.I.

You are investigating the thermoreceptive striking behavior of the Western Diamondback Rattlesnake (*Crotalus atrox*). Using robotic prey, you recorded the `Strike_Latency_sec` (how fast the snake strikes) across three different target temperatures: **25°C (Ambient), 37°C (Mouse Temp), and 40°C (Bird Temp)**.

**Your Tech:** Your "AI Grad Student" (Copilot/Gemini via Positron/Antigravity).

**The Challenge:** You must direct your AI to analyze this data. But remember: your AI does not know what a pit viper is. It has not read Chapter 5 of *An Immense World*. If you do not provide biological guardrails, the AI will make "human-centric" assumptions and ruin the analysis.

---

## Step 1: Load the Data

As the Principal Investigator, you must direct your AI to pull the data directly from our repository. In the Positron Assistant chat, instruct the AI to write the R code to load the PitViper_Thermal_Strikes.csv dataset and display the first few rows.

Give the AI this exact URL to read from:
https://raw.githubusercontent.com/djtobiansky/StatsLab/refs/heads/main/NEU474/PitViper_Thermal_Strikes.csv

Once the AI generates the correct script, paste that code into the R chunk below and run it in your console to verify the data loaded correctly. Ensure the AI annotates each line of code so you can follow along. Make a new markdown file (or download this file) and copy and paste this entire document and save it into your new folder (ask Dr. T if you are struggling here). 

# Define the URL for the dataset
url <- "https://raw.githubusercontent.com/djtobiansky/StatsLab/refs/heads/main/NEU474/PitViper_Thermal_Strikes.csv"

# Load the dataset into a data frame
pitviper_data <- read.csv(url)

# Display the first few rows of the dataset
head(pitviper_data)


---

## Step 2: The Naive Analysis (The Trap)

First, let's see what happens when we let the AI run wild without biological context. Instruct your AI to run a One-Way ANOVA comparing `Strike_Latency_sec` across the three `Target_Temp` groups using the *entire* dataset. Ask it to generate a boxplot of these results using `ggplot2`.

Paste the code below, run it, and save the resulting messy graph. Ensure the AI annotates each line of code so you can follow along. 

# Load necessary library
library(ggplot2)
# Load the dataset directly from the URL
url <- "https://raw.githubusercontent.com/djtobiansky/StatsLab/refs/heads/main/NEU474/PitViper_Thermal_Strikes.csv"
pitviper_data <- read.csv(url)
# Convert the Target_Temp to a factor for the ANOVA and boxplot
pitviper_data$Target_Temp <- as.factor(pitviper_data$Target_Temp)
# Run One-Way ANOVA
anova_results <- aov(Strike_Latency_sec ~ Target_Temp, data = pitviper_data)
cat("\n--- One-Way ANOVA Results ---\n")
print(summary(anova_results))
# Generate boxplot using ggplot2
boxplot_plot <- ggplot(pitviper_data, aes(x = Target_Temp, y = Strike_Latency_sec, fill = Target_Temp)) +
  geom_boxplot(alpha = 0.7) +
  theme_minimal() +
  labs(
    title = "Strike Latency Across Target Temperatures",
    x = "Target Temperature",
    y = "Strike Latency (sec)"
  ) +
  scale_fill_brewer(palette = "Set2") + # Makes the plot colors colorblind-friendly
  theme(legend.position = "none")
# Save the boxplot as a PNG image
ggsave("strike_latency_boxplot_updated.png", plot = boxplot_plot, width = 7, height = 5, dpi = 300)
```


### P.I. Reflection: Why is this wrong?

*Based on Chapter 5 of Ed Yong's text, explain why the graph above looks like a messy, non-significant blob. What variable did the AI ignore, and how does that violate the pit viper's Umwelt?*

**[Ch 5 explains that the data graph appears as a mess because AI models do not account for thermal contrast automatically. It says that instead they focus on absolute temperature, overlooking the umwelt of the pit vipers. Since pit vipers perceive thermal radiation for their surroundings, the AI falls short by disregarding this bioloigcal advantage unique to their umwelt.]**

---

## Step 3: The "Umwelt-Corrected" Analysis

Now, act as the Principal Investigator. You must correct your AI Grad Student.

**Your Prompt Log:**
*What exact prompt did you use in the AI chat to tell it to filter the data based on the background environment before re-running the stats?*

**[Type your prompt here. E.g., "Wait, pit vipers rely on thermal..."]**

Have your AI write the code to filter the dataset to only include trials on `20C_Cool_Soil`. Then, run the One-Way ANOVA again, apply a Tukey HSD correction, and plot the clean, corrected data. Ensure the AI annotates each line of code so you can follow along. 

```R
# # 1. Load the ggplot2 library to handle our data visualizations
library(ggplot2)
# 2. Inform R where the raw CSV data lives on the internet
url <- "https://raw.githubusercontent.com/djtobiansky/StatsLab/refs/heads/main/NEU474/PitViper_Thermal_Strikes.csv"
# 3. Read the CSV data directly from the URL into a dataframe named 'pitviper_data'
pitviper_data <- read.csv(url)
# 4. Filter the dataset to isolated trials on '20C_Cool_Soil'
# We use the 'subset' function so we only keep the rows with this exact background
filtered_data <- subset(pitviper_data, Background_Environment == "20C_Cool_Soil")
# 5. Convert 'Target_Temp' to a factor (a categorical variable in R)
# ANOVA requires our independent variable to be treated as categories, not raw text
filtered_data$Target_Temp <- as.factor(filtered_data$Target_Temp)
# 6. Run the One-Way ANOVA using the 'aov' function
# We are testing if 'Strike_Latency_sec' (dependent) varies by 'Target_Temp' (independent)
anova_results <- aov(Strike_Latency_sec ~ Target_Temp, data = filtered_data)
# Print a header and the ANOVA result summary to the console
cat("\n--- One-Way ANOVA Results (Filtered: 20C_Cool_Soil) ---\n")
print(summary(anova_results))
# 7. Run a Tukey Honest Significant Difference (HSD) test using the ANOVA model
# This post-hoc test will tell us *which specific groups* differ significantly from each other
tukey_results <- TukeyHSD(anova_results)
# Print a header and the Tukey HSD result summary to the console
cat("\n--- Tukey HSD Post-Hoc Test Results ---\n")
print(tukey_results)
# 8. Generate the finalized boxplot using our filtered dataset
boxplot_plot <- ggplot(filtered_data, aes(x = Target_Temp, y = Strike_Latency_sec, fill = Target_Temp)) +
  # Add the boxplot geometric layer with slight transparency (alpha = 0.7)
  geom_boxplot(alpha = 0.7) +
  # Apply a clean, modern theme that removes distracting background elements
  theme_minimal() +
  # Provide extremely clear labels for the plot title and both axes
  labs(
    title = "Strike Latency by Target Temp (Background: 20C Cool Soil)",
    x = "Target Temperature",
    y = "Strike Latency (sec)"
  ) +
  # Use a qualitative colorblind-friendly palette from ColorBrewer
  scale_fill_brewer(palette = "Set2") + 
  # Hide the legend since the x-axis already contains the category labels
  theme(legend.position = "none")
# 9. Save the finalized ggplot object as an image file on your computer
ggsave("strike_latency_filtered_boxplot.png", plot = boxplot_plot, width = 7, height = 5, dpi = 300)


```

---

## Step 4: Final Statistical Conclusion

*Look at the output of your corrected Tukey HSD. As the P.I., summarize the findings in one or two sentences (your own words; not the AI's). Is there a statistically significant difference in strike latency between a 37°C target and a 40°C target? What about effect size? *

**[This is a highly significant difference in strike latency across the temp groups. The pit viper strike signifcantly faster at the mouse and bird compared to the ambient temperature. However, there was no significant difference between strike latency of the mouse vs bird.]**

---

**Submission Instructions:**
To submit this lab:

1. Open the Markdown Preview in Positron (usually `Ctrl+Shift+V` or `Cmd+Shift+V`).
2. Right-click the preview and select "Print" or "Export" to save it as a PDF.
3. Upload the PDF and your final corrected boxplot image to Canvas.
