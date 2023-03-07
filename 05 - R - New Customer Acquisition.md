# New Customer Acquisition

```R
library(plotly)
library(ggplot2)
library(dplyr)
library(lubridate)

data <- read.csv("LOD4R/LOD05.csv", header = TRUE, sep = "\t")

data1 <- data %>% 
  mutate(OrderDate = as.Date(OrderDate)) %>%
  group_by(CustomerID) %>%
  summarise(FirstPurchase = min(OrderDate))

data2 <- data %>%
  mutate(OrderDate = as.Date(OrderDate)) %>%
  left_join(data1, by = "CustomerID") %>%
  filter(OrderDate == FirstPurchase) %>%
  group_by(Market, OrderDate) %>%
  summarise(DistCnt = n_distinct(CustomerID), .groups = "drop") %>%
  group_by(Market) %>%
  mutate(RunningTotal = cumsum(DistCnt)) %>%
  select(-DistCnt)

ggplotly(
  ggplot(data2, aes(x = OrderDate, y = RunningTotal, color = Market, group = Market)) +
    geom_line(size = 1.02, aes(text = paste("On ", format(OrderDate, "%Y-%m-%d"), ", ", Market, " acquired a total of ", RunningTotal, " customers."))) +
    ggtitle("New Customer Acquisition") +
    labs(x = "Day of Order Date", y = "Total Customers") +
    scale_x_date(date_labels = "%Y-%m-%d") +
    # theme(legend.position = "top") +
    theme(plot.title = element_text(size = 14, face = "bold", color = "black"))
, tooltip = "text")
```

# Result

![R05](https://user-images.githubusercontent.com/79496040/223540179-9e1c7f68-e275-4c28-a8f7-2be55880b670.gif)

# Comment

Adjust legend position
