# Return Purchase by Cohort

```R
library(lubridate)
library(dplyr)
library(zoo)
library(reshape2)
library(forcats)
library(echarts4r)
library(htmlwidgets)

data <- read.csv("LOD4R/lod10.csv", header = TRUE, sep = ",")

First_Purchase <- data %>%
  mutate(OrderDate = as.Date(OrderDate)) %>%
  group_by(CustomerID) %>%
  summarise(FirstPurchase = min(OrderDate))

Return_Purchase <- unique(data[,c("CustomerID","OrderDate")]) %>%
  mutate(OrderDate = as.Date(OrderDate)) %>%
  group_by(CustomerID) %>%
  slice(2) %>%
  rename(ReturnPurchase = OrderDate) 

combined <- left_join(First_Purchase, Return_Purchase, by = "CustomerID") 

res <- combined %>%
  mutate(YearQrt = paste0(year(FirstPurchase)," Q",quarter(FirstPurchase)),
         Diff = (year(ReturnPurchase) - year(FirstPurchase))*4 + quarter(ReturnPurchase) - quarter(FirstPurchase),
         Diff_ = factor(ifelse(is.na(Diff), "Lapsed", Diff ), levels = c("Lapsed", 0:15))              
         ) %>%
  group_by(YearQrt, Diff_) %>%
  summarise(DistCnt = n_distinct(CustomerID), .groups = 'drop') %>%
  arrange(desc(YearQrt))


res %>%
  e_charts(Diff_) %>%
  e_heatmap(y = YearQrt, z = DistCnt) %>%
  e_title("Return Purchase by Cohort") %>%
  e_x_axis(name = "Quarter to Repeat Purchase", 
           nameLocation = "center", 
           nameGap = 30,
           axisTick = list(show = FALSE),
           position = "top",
           type = "category") %>%
  e_y_axis(name = "Quarter of 1st Purchase", 
           axisTick = list(show = FALSE),
           nameGap = 20
           ) %>%
  e_visual_map(DistCnt,
               show = FALSE,
               inRange = list(color = c("#b8e2d7", "#77bec8", "#368eae", "#2c5985"))) %>%
  e_tooltip(formatter = JS(
    "function(params) {
      return 'Among customers who first made a purchase in <b>' + params.value[1] + '</b>,</br> <b>' + params.value[2] + '</b> waited <b>' + params.name + '</b> quarters until their second purchase.';
    }"
  ))

```

# Result

![R10_v2](https://user-images.githubusercontent.com/79496040/230251758-79fe1b7d-f84a-4754-bbd3-b2e5de173c0f.gif)
