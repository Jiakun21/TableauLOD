# Daily Profit KPI

```R
library(echarts4r)
library(dplyr)
library(lubridate)
library(htmlwidgets)

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

# set the colors for ProfitKPI
colors <- c("#93aeca", "#d3d3d3", "#f7ba7e")

# Create a list of charts for each ProfitKPI
i <- 1
charts <- list()
for (p in unique(DailyProfit$ProfitKPI)) {
  df <- DailyProfit[DailyProfit$ProfitKPI == p,]
  chart <- e_charts(df, x = yymm, height = 230) %>%
    e_area(Cnt, symbolSize = 3) %>%
    e_legend(show = FALSE) %>%
    e_title(text = ifelse(i == 1, "Daily Profit KPI", "")) %>%
    e_tooltip(formatter = JS(
          "function(params) {
              const dateStr = params.value[0];
              const date = new Date(dateStr);
              const year = date.getFullYear();
              const month = date.toLocaleString('default', { month: 'long' });
              const formattedDate = `${year}, ${month}`;
              return 'In <b>' + formattedDate + '</b> has <b>' + params.value[1] + '</b> days.';
          }"
    )) %>%
    e_color(colors[i]) %>%
    e_draft(text = p, size = "100px") %>%
    e_x_axis(axisLabel = list(formatter = '{yyyy} {MMM}', rotate = 90)) %>%
    e_y_axis(name = "Num of Days", nameLocation = "center", nameGap = 40, splitLine = list(show = FALSE))
  i <- i + 1
  charts[[p]] <- chart
}

e_arrange(charts) 
  
```

# Result

![R03_v2](https://user-images.githubusercontent.com/79496040/229628748-39ddd32e-ddd3-47a8-bd81-adfebc29d805.gif)

