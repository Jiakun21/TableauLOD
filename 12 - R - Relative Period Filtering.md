# Relative Period Filtering

```R
library(echarts4r)
library(dplyr)
library(lubridate)
library(tidyr)

data <- read.csv("LOD4R/lod12.csv", header = TRUE, sep = ",")

data <- data %>%
  mutate(OrderDate = as.Date(OrderDate),
         Year = year(OrderDate),
         WeekNum = week(OrderDate),
         DayofYear = yday(OrderDate)) %>%
  filter(DayofYear <= yday(max(data$OrderDate))) %>%
  group_by(Year, WeekNum) %>%
  summarise(Profit = round(sum(Profit),2)) %>%
  mutate(Profit_RT = cumsum(Profit))


# create area chart
area_data <- pivot_wider(data, id_cols = WeekNum, names_from = Year, values_from = Profit_RT)
colnames(area_data) <- c("WeekNum", "PreYear", "CurYear")

area_data <- area_data %>%
  mutate(Diff = round(CurYear - PreYear,2))

area_plot <- area_data %>% 
  e_charts(WeekNum, height = 350) %>%
  e_area(Diff, symbol = '') %>%
  e_title("Relative Period Filtering") %>%
  e_color(color = "#17becf") %>%
  e_legend(show = FALSE) %>%
  e_x_axis(axisLabel = list(show = FALSE)) %>%
  e_y_axis(name = "YoY Difference in Cumulative Profit") %>%
  e_tooltip(formatter = htmlwidgets::JS("
            function(params) {
              var tooltip = 'The difference in year to date profit in';
              tooltip += ' <b>Week ' + params.value[0] + '</b> is <b>$' + params.value[1] + '</b>.';
              return tooltip;
          }
        "))


# create line chart
line_plot <- data %>%
  e_charts(WeekNum, height = 350) %>%
  e_line(Profit_RT, symbol = '') %>%
  e_color(color = c("#ff7f0f","#ffd94a")) %>%
  e_tooltip(trigger = "item",
            formatter = htmlwidgets::JS("
            function(params) {
              console.log(params);
              return 'Year to date sales in <b>week ' + params.value[0] + '</b> of <b>' + params.seriesName + '</b> are <b>$' + params.value[1] + '</b>. ';
          }
        ")) %>%
  e_y_axis(name = "Cumulative Profit")

e_arrange(area_plot, line_plot, rows = 2) 

```

# Result

![R12](https://user-images.githubusercontent.com/79496040/227360195-acd5b3c5-373a-4180-8bc3-fd73cde4d718.gif)


