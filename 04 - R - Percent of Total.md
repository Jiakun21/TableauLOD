# Percent of Total

```R
library(plotly)

# read in data
data <- read.csv("LOD4R/lod04.csv", header = TRUE, sep = "\t")

Total_Sales <- sum(data$Sales)
Total_Pct <- data %>% 
  group_by(Market, Country) %>% 
  summarise(Sales = sum(Sales), .groups = "drop") %>% 
  mutate(Pct = round(Sales/Total_Sales*100)) 
Total_Pct <- Total_Pct[,-3]

# Create a choropleth map with plotly
fig <- plot_geo(Total_Pct, locationmode = "country names") %>% 
  add_trace(z = ~Pct, locations = ~Country, 
            text = ~paste("<b> ", Country, "</b> represents <b>", Pct, "% </b> of total sales globally."), 
            type = "choropleth", 
            c("#c9e9e0", "#a9d8d8", "#8bc7d0", "#69b4c7", "#5da0bb", "#5a8dac", "#557a9d"),
            hovertemplate = "%{text}<extra></extra>") %>%
  layout(title = "Total of Percent", geo = list(scope = "world"))

# Show the plot
fig
```

# Result

![R04](https://user-images.githubusercontent.com/79496040/223325460-4e059642-6ead-4ea1-be36-44d537021aef.gif)

# Comment

Will add dropdown list
