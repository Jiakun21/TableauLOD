# Return Purchase by Cohort

```R
library(lubridate)
library(dplyr)
library(zoo)
library(reshape2)
library(forcats)
library(ggplot2)
library(plotly)

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
  mutate(YearQrt = as.yearqtr(FirstPurchase),
         Diff = factor((year(ReturnPurchase) - year(FirstPurchase))*4 + quarter(ReturnPurchase) - quarter(FirstPurchase),
                       levels = c(NA, 0:15))) %>%
  group_by(YearQrt, Diff) %>%
  summarise(DistCnt = n_distinct(CustomerID), .groups = 'drop') 


ggplotly(
  ggplot(data = res, 
         aes(x = fct_rev(factor(Diff, levels = c(NA, 0:15))),
             y = YearQrt, 
             fill = DistCnt)) + 
    geom_tile(aes(text = paste0("Among customers who first made a purchase in <b>", YearQrt, "</b>, <b>", DistCnt, "</b> waited <b>", Diff, "</b> quarters until their second purchase."))) + 
    ggtitle("Return Purchase by Cohort") + 
    xlab("Quarter to Repeat Purchase") +
    ylab("Quarter of First Purchase") +
    scale_fill_gradient(low = "#b8e2d7", high = "#2c5985") +
    scale_y_reverse() +
    # coord_flip() +
    theme(legend.position = "none", 
          axis.title.x.top = element_text(),
          axis.text.x.top = element_text(angle = 180, hjust = 1)),
  tooltip = "text")

```

# Result

![R10](https://user-images.githubusercontent.com/79496040/226630155-ca9897e5-c86d-4acf-b158-f1ec1f4f3842.gif)

# Comment
Will adjust x axis position to the top </br>
Will adjust x axis label order
