# New Customer Acquisition

```R
library(echarts4r)
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

line_chart <- data2 %>%
  e_charts(OrderDate) %>%
  e_line(RunningTotal, symbol = 'circle') %>%
  e_tooltip(
    trigger = "item",
    formatter = htmlwidgets::JS("
          function(params) {
            var content = 'On <b>' + params.value[0] + '</b>, ';
            content += ' <b>' + params.seriesName + '</b> had acquired a total of  <b>'  + params.value[1] + '</b> customers.';
            return content;
          }
        ")
  ) %>%
  e_title("New Customer Acquisition") %>%
  e_y_axis(name = "Total Customers", 
           nameLocation = "center",
           nameGap = 40,) %>%
  e_x_axis(name = "Day of Order Date", 
           nameLocation = "center",
           nameGap = 40,
           type = "time") 

line_chart
```

# Result

![R05_v2](https://user-images.githubusercontent.com/79496040/229307826-f378f772-65ae-42e6-bd90-2927972450b7.gif)


