# Proportional Brushing

```R
library(dplyr)
library(shiny)
library(echarts4r)
library(shinyjs)
library(htmlwidgets)


data <- read.csv("lod14.csv", header = TRUE, sep = ",")

map_data <- data %>%
  group_by(Country) %>%
  summarise(Sales = sum(Sales))


bar_data1 <- data %>%
  rename(SubCat = Sub.Category) %>%
  group_by(Market, SubCat) %>%
  summarise(Sales_SubCat = sum(Sales), .groups = "drop")

bar_data2 <- data %>%
  rename(SubCat = Sub.Category) %>%
  group_by(Market, SubCat, Country) %>%
  summarise(Sales_SubCat_Ctry = sum(Sales), .groups = "drop")

res <- left_join(bar_data2, bar_data1, by = c("Market", "SubCat")) %>%
  mutate(Pct = Sales_SubCat_Ctry/Sales_SubCat)


# Create map chart
map_chart <- map_data %>%
  e_charts(Country) %>%
  e_map(Sales, map = "world") %>%
  e_title("Proportional brushing") %>%
  e_visual_map(Sales, 
               # dimension = 0,
               inRange = list(color = c("#ba684e", "#fcb76b", "#aad2e8", "#5882aa")),
               show = FALSE) %>%
  e_tooltip(trigger = "item",
            formatter = htmlwidgets::JS("
                              function(params) {
                                var content = 'Country: <b>' + params.name + '</b> </br> ';
                                content += 'Sales: <b>$' + params.value + '</b>';
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
    column(width = 8, div(style = "height: 400px;", map_chart))
      ),
  fluidRow(
    br(),
    br()
  ),
  fluidRow(
    column(width = 6, echarts4rOutput("left_bar", height = "350px")),
    column(width = 5, echarts4rOutput("right_bar", height = "350px"))
      )
)


# Define server logic
server <- function(input, output) {
  
  # Define a reactive expression to filter the data based on the selected state
  filtered_data <- reactive({
    if (!is.null(input$selected_country)) {
      res %>%
        filter(Country == input$selected_country)
    } else {
      res
    }
  })
  
  
  # Update the left bar chart when the user selects a country 
  output$left_bar <- renderEcharts4r({

    bar_title <- ifelse(is.null(input$selected_country), 'None', input$selected_country)
    market_title <- ifelse(is.null(input$selected_country), 'None', filtered_data()$Market)

    left_bar <- filtered_data() %>%
      arrange(Sales_SubCat) %>%
      e_charts(SubCat) %>%
      e_bar(Sales_SubCat) %>%
      e_bar(Sales_SubCat_Ctry, barGap = "-100%") %>%
      e_color(c("#9fe5ef","#39b0c2")) %>%
      e_flip_coords() %>%
      e_y_axis(interval = 0) %>%
      e_title(text = paste0("Sales by Product Sub-Category in ", bar_title, " vs Total Sales in ", market_title)) %>%
      e_legend(show = TRUE,
               top = "7%",
               left = "7%") %>%
      # e_tooltip(trigger = "axis") %>%
      e_x_axis(name = "Sales",
               nameLocation = "center",
               nameGap = 50,
               # max = floor(max(filtered_data()$Sales_SubCat)/100000)*1.1*100000,
               formatter = e_axis_formatter("currency")
               )

  })
  
  
  # Update the right bar chart when the user selects a country 
  output$right_bar <- renderEcharts4r({
   
    right_bar <- filtered_data() %>%
      arrange(Sales_SubCat) %>%
      e_charts(SubCat) %>%
      e_bar(Pct) %>%
      e_color("#dcddde") %>%
      e_flip_coords() %>%
      e_y_axis(show = FALSE) %>%
      e_legend(show = FALSE) %>%
      e_labels(position = "right", 
               formatter = htmlwidgets::JS("
                              function(params) {
                                return (params.value[0]*100).toFixed(2) + '%';
                              }
                                            ")) %>%
      e_tooltip(trigger = "item",
                formatter = htmlwidgets::JS("
                              function(params) {
                                var content = '<b> ' ;
                                var value = (params.value[0]*100).toFixed(2);
                                content += '</b> Generate <b>' + value + '%</b> of total <b>' + params.name + '</b> sales in <b>APAC</b>';
                                return content;
                              }
            ")) %>%
      e_x_axis(name = "Percent of Total",
               nameLocation = "center",
               nameGap = 50,
               formatter = e_axis_formatter("percent", digits = 0)
      )

  })
  
}


# Run the application
shinyApp(ui = ui, server = server)

```

# Result

![R14](https://user-images.githubusercontent.com/79496040/228011332-ec423851-b27b-4c2a-973e-5629616bc71b.gif)

# Comment
Will add custom tooltips in both bar plots </br>
Will customize the layout when no country is selected
