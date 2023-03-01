# Customer Order Frequency

```R
library(dplyr)

data <- read.csv("/lod4rdemo/lod01.csv", header = TRUE, sep = ";")

Orders_per_Customer <- data %>% group_by(CustomerID) %>% summarise(Total_Orders = n_distinct(OrderID))
Orders_Freq <- Orders_per_Customer %>% group_by(Total_Orders) %>% summarise(Freq = n_distinct(CustomerID))

Orders_Freq %>% plot_ly(x = ~Total_Orders, y = ~Freq, type = 'bar',
                        hoverinfo = "text",
                        text = ~paste("<b>", Freq,"</b> customers have made <b>", Total_Orders, "</b> purchases.")) %>% 
                add_text(x = ~Total_Orders, y = ~Freq, text = ~Freq, textposition = "outside") %>%
                layout(title = "Customer Order Frequency", 
                       xaxis = list(title = "Number of Orders Placed"), 
                       yaxis = list(title = "Number of Customers"))
```

# Result

![R01](https://user-images.githubusercontent.com/79496040/222274722-05063de9-fb39-453d-bf32-77d972207681.gif)
