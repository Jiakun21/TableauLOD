# Customer Order Frequency

```R
library(dplyr)
library(echarts4r)
library(htmlwidgets)

data <- read.csv("LOD4R/lod01.csv", header = TRUE, sep = ";")

Orders_per_Customer <- data %>% 
  group_by(CustomerID) %>% 
  summarise(Total_Orders = n_distinct(OrderID))

Orders_Freq <- Orders_per_Customer %>% 
  group_by(Total_Orders) %>% 
  summarise(Freq = n_distinct(CustomerID))

Orders_Freq %>%
  e_charts(Total_Orders) %>%
  e_bar(Freq) %>%
  e_legend(show = FALSE) %>%
  e_labels(show = TRUE, position = "top") %>%
  e_visual_map(Freq, 
               inRange = list(color = c("#b2d4db", "#79c2df", "#4a93bf", "#2d4b73")),
               show = FALSE) %>%
  e_tooltip(formatter = JS("
            function(params) {
              var content = '<b>' + params.value[1] + '</b> customers have made <b>' + params.value[0] + '</b> purchases.';
              return content;
                            }")) %>%
  e_title("Customer Order Frequency", "How many customers have made 1, 2, 3, N orders?") %>%
  e_x_axis(name = "Number of Orders Placed", nameLocation = "center", nameGap = 40, splitLine = list(show = FALSE)) %>%
  e_y_axis(name = "Number of Customers", nameLocation = "center", nameGap = 40, splitLine = list(show = FALSE))

```

# Result

![R01_v2](https://user-images.githubusercontent.com/79496040/230176668-368413f6-8660-485b-8ce6-f6d885ef969a.gif)

