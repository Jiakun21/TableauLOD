# Average of Top Deals by Sales Rep

```R
library(dplyr)
library(shiny)
library(echarts4r)
library(shinyjs)
library(htmlwidgets)

data <- read.csv("LOD07.csv", header = TRUE, sep = ",")


# Max Sales per Country per Sales Rep
temp <- data %>%
  rename(SalesRep = Sales.Rep) %>%
  group_by(Country, SalesRep) %>%
  summarise(MaxSales_Ctry_Rep = max(Sales), .groups = "drop")


# Define the map data
map_data <- temp %>%
  group_by(Country) %>%
  summarise(MeanSales = round(mean(MaxSales_Ctry_Rep)))


# Create world filled map
world_map <- map_data %>%
  e_charts(Country) %>%
  e_map(MeanSales, map = "world") %>%
  e_visual_map(MeanSales, 
               inRange = list(color = c("#ba684e", "#fcb76b", "#aad2e8", "#5882aa"))) %>%
  e_title("Average of Top Deals by Sales Rep") %>%
  e_tooltip(trigger = "item",
            formatter = htmlwidgets::JS("
                              function(params) {
                                var content = '<b>' + params.name + '</b>`s ';
                                content += 'average largest deal size is $<b>' + params.value + '</b>';
                                Shiny.setInputValue('selected_country', params.name);
                                return content;
                              }
            ")) %>%
  e_on(
    query = "series.bar",
    # Set input values
    handler = "function(params){
                  Shiny.setInputValue('selected_country', params.name, {priority: 'event'});
       }",
    event = "click"
  )


# Define UI
ui <- fluidPage( 
  useShinyjs(),
  fluidRow(
    column(width = 8, world_map)
      ),
  fluidRow(
    column(width = 12, echarts4rOutput("bar_chart", height = "300px"))
      )
)


# Define server logic
server <- function(input, output) {
  
  # Define a reactive expression to filter the data based on the selected country
  filtered_data <- reactive({
    if (!is.null(input$selected_country)) {
      temp %>%
        filter(Country == input$selected_country)
    } else {
      temp
    }
  })
  
  # Update the bar chart when the user selects a country from the world map
  output$bar_chart <- renderEcharts4r({
    
    bar_title <- ifelse(is.null(input$selected_country), 'All', input$selected_country)
    
    bar_data <- filtered_data() %>%
      group_by(SalesRep) %>%
      summarise(TopSales = max(MaxSales_Ctry_Rep, na.rm = TRUE)) %>%
      arrange(TopSales) %>%
      mutate(color = ifelse(TopSales > mean(TopSales), '#90cee3', '#ffbb78'))
    
    bar_chart <- bar_data %>%
      e_charts(SalesRep) %>%
      e_bar(TopSales) %>%
      e_title(text = paste0("Top Deal by Sales Rep in ", bar_title, " Compared to National Average")) %>%
      e_add_nested("itemStyle", color) %>%
      e_legend(show = FALSE) %>%
      e_flip_coords() %>%
      e_mark_line(data = list(y = mean(bar_data$TopSales), type = "average"),
                  label = list(formatter = ""),
                  lineStyle = list(color = "#000000", type = "dashed", width = 1)) %>%
      e_tooltip(
        trigger = "axis",
        formatter = htmlwidgets::JS("
        function(params) {
          var tooltip = '';
          for (var i = 0; i < params.length; i++) {
            tooltip +=  params[i].name + '`s largest deal size is $' + params[i].value[0] + ' compared to the average line'; 
          }
          return tooltip;
        }
      ")
      )
    
    bar_chart
  })
  
}


# Run the application
shinyApp(ui = ui, server = server)

```

# Result

![R07](https://user-images.githubusercontent.com/79496040/225428277-45eefdd4-ff74-4d0f-a457-dcc959ce1ae4.gif)


# Comment

Will add the average to the tooltips
