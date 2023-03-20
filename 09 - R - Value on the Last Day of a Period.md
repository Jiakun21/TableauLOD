# Value on the Last Day of a Period

```R
library(dplyr)
library(lubridate)
library(zoo)
library(tidyverse)
library(ggplot2)
library(plotly)

data <- read.csv("LOD4R/LOD09.csv", header = TRUE, sep = "\t")

data <- data %>% 
  mutate(Date = as.Date(Date),
         YearMon = as.yearmon(Date)) %>%
  group_by(Ticker, YearMon) %>%
  summarise(Avg_Price = round(mean(Close),2),
            LastDay_Price = last(Close),
            LastDay = last(Date),
            .groups = 'drop') %>%
  pivot_longer(cols = c(Avg_Price, LastDay_Price), 
               names_to = "PriceType", 
               values_to = "Price")


# create custom color palette
ticker_colors <- c("#18becf", "#ffc156", "#ff7f0e", "#9edae5", "#ffdd71", "#ffbb78")


# plot line chart based on different tickers
ggplotly(
  ggplot(data, 
         aes(x = LastDay, 
             y = Price, 
             color = Ticker, 
             linetype = PriceType,
             group = Ticker)) +
    geom_line(size = 1.05, 
              aes(text = paste("The average close value of <b>", Ticker, "</b> in <b>", format(LastDay, "%B %Y"), "</b> was <b>$", Price, "</b> compared to a close value on <b>", format(LastDay, "%m/%d/%Y"), "</b>."))) +
    ggtitle("Value on the last day of a period") +
    labs(x = "Month of Date", y = "Value", color = "Ticker") +
    scale_x_date(date_breaks = "1 month", date_labels = "%b %Y") +
    scale_color_manual(values = setNames(ticker_colors, unique(data$Ticker))) +
    theme_bw()+
    theme(panel.grid.major = element_blank(),
          panel.grid.minor = element_blank(),
          legend.position = "bottom"),
tooltip = "text")
```

# Result

![R09](https://user-images.githubusercontent.com/79496040/226378575-3b4d30a1-29e4-4933-a4aa-0c45d05a5817.gif)

# Comment
Enhancement: Tooltips and Legend location

