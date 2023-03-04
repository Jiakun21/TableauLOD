# Daily Profit KPI

```R
library(plotly)
library(ggplot2)
library(dplyr)
library(lubridate)

data <- read.csv("LOD4R/lod03.csv", header = TRUE, sep = "\t")

DailyProfit <- data %>% 
  mutate(OrderDate = as.Date(OrderDate)) %>%
  group_by(OrderDate) %>% summarise(Profit = sum(Profit)) %>%
  mutate(ProfitKPI = case_when(
                          Profit > 2000 ~ "Highly Profitable",
                          Profit < 0 ~ "Unprofitable",
                          TRUE ~ "Profitable"),
         year = year(OrderDate),
         monthNum = month(OrderDate),
         monthTxt = month(OrderDate, label = TRUE, abbr = FALSE)) %>%
  group_by(ProfitKPI, year, monthNum, monthTxt) %>%
  summarise(Cnt = n_distinct(OrderDate)) %>%
  mutate(yymm = as.Date(paste(year, monthNum,"01", sep = "-")))

ggplot(DailyProfit, aes(x = yymm, y = Cnt, fill = ProfitKPI)) +
  geom_area(alpha = 0.7) +
  scale_fill_manual(values = c("#93aeca", "#f7ba7e", "#ed999a")) +
  facet_grid(rows = vars(ProfitKPI), scales = "free_y") +
  scale_x_date(date_breaks = "1 month", date_labels = "%b %Y", expand = c(0,0)) +
  labs(title = "Daily Profit KPI",
       x = "",
       y = "Num of Days") +
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust=1),
        legend.position = "top", legend.direction = "horizontal") 
  
```

# Result

![R03](https://user-images.githubusercontent.com/79496040/222861570-3bc7129c-adbd-4a45-b5d2-dd45da2d2b0a.png)

# Comment

Will add tooltips feature
