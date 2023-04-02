# Percent of Total

```R
library(echarts4r)
library(shiny)
library(dplyr)

# read in data
data <- read.csv("lod04.csv", header = TRUE, sep = "\t")

Total_Sales <- sum(data$Sales)
Total_Pct <- data %>% 
  group_by(Market, Country) %>% 
  summarise(Sales = sum(Sales), .groups = "drop") %>% 
  mutate(Pct = round(Sales/Total_Sales*100)) 


# set up ui
ui <- fluidPage(
  selectInput("market", "Market", choices = unique(Total_Pct$Market)),
  echarts4rOutput("map")
)

# set up server
server <- function(input, output) {  
  # filter data by selected market
  filtered_data <- reactive({
    Total_Pct %>% filter(Market == input$market)
  })
  
  # create map
  output$map <- renderEcharts4r({ 
    filtered_data() %>% 
      e_charts(Country) %>% 
      e_map(Pct, map = "world") %>% 
      e_visual_map(Pct,
                   orient = "horizontal",
                   bottom = 10,
                   left = 600,
                   inRange = list(color = c("#c9e9e0", "#a9d8d8", "#8bc7d0", "#69b4c7", "#5da0bb", "#5a8dac", "#557a9d"))
                   ) %>% 
      e_tooltip(formatter = htmlwidgets::JS("
                function(params) {
                  var content = '<b>' + params.name + '</b> represents <b>';
                  content += params.value + '%</b> of total sales globally.';
                  return content;
                }
                 ")) %>%
      e_title("Total of Percent")    
  })  
}

# run app
shinyApp(ui = ui, server = server)
```

# Result

![R04_v2](https://user-images.githubusercontent.com/79496040/229325953-cb5e7c20-8352-45aa-9c04-4880403d5926.gif)

