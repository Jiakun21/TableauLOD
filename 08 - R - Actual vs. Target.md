# Actual vs. Target

```R
library(dplyr)
library(shiny)
library(echarts4r)
library(shinyjs)
library(htmlwidgets)

data <- read.csv("LOD08.csv", header = TRUE, sep = "\t")

# left_bar
left_data <- data %>% 
  group_by(State) %>% 
  summarise(Diff = sum(Profit - TargetProfit)) %>% 
  arrange(Diff)

# right_bar
right_data <- data %>%
  group_by(State, Product) %>%
  summarise(Diff_Prod = sum(Profit - TargetProfit), .groups = 'drop') %>%
  mutate(Num_Prod_Profit_GE_Target = ifelse(Diff_Prod > 0, 1, 0)) %>%
  group_by(State) %>%
  summarise(Pct_Prod_Profit_GT_Target = sum(Num_Prod_Profit_GE_Target)/n_distinct(Product))

# left_join
combined <- left_join(left_data, right_data, by = "State")


# Create left bar chart
left_bar <- combined %>%
  e_charts(State) %>%
  e_bar(Diff) %>%
  e_title("Actual vs. Target") %>%
  e_visual_map(Diff, 
               dimension = 0,
               inRange = list(color = c('#e16a31', '#fb9a57', '#afd3de','#8ecbe4', '#4e6789')),
               show = FALSE) %>%
  e_legend(show = FALSE) %>%
  e_flip_coords() %>%
  e_tooltip(trigger = "item",
            formatter = htmlwidgets::JS("
                              function(params) {
                                var content = 'In <b>' + params.name + '</b> ';
                                content += 'the difference between profit and target profit is <b>$' + params.value[0] + '</b>';
                                Shiny.setInputValue('selected_state', params.name);
                                return content;
                              }
            ")) %>%
  e_on(
    query = "series.bar",
    # Set input values
    handler = "function(params){
                  Shiny.setInputValue('selected_state', params.name, {priority: 'event'});
       }",
    event = "click"
  ) %>%
  e_x_axis(name = "Difference between Profit and Target",
           nameLocation = "center",
           nameGap = 40,
           formatter = e_axis_formatter("currency")) 


# Create right bar chart
right_bar <- combined %>%
  e_charts(State) %>%
  e_bar(Pct_Prod_Profit_GT_Target) %>%
  e_color(color = "#d0d0d0") %>%
  e_legend(show = FALSE) %>%
  e_flip_coords() %>%
  e_y_axis(show = FALSE) %>%
  e_tooltip(trigger = "item",
            formatter = htmlwidgets::JS("
                              function(params) {
                                var content = 'In <b>' + params.name + '</b> ';
                                var value = (params.value[0]*100).toFixed(1);
                                content += 'the percentage of products above target is <b>' + value + '%</b>';
                                Shiny.setInputValue('selected_state', params.name);
                                return content;
                              }
            ")) %>%
  e_on(
    query = "series.bar",
    # Set input values
    handler = "function(params){
                  Shiny.setInputValue('selected_state', params.name, {priority: 'event'});
       }",
    event = "click"
  ) %>%
  e_x_axis(name = "Percentage of Products above Target",
           nameLocation = "center",
           nameGap = 40,
           formatter = e_axis_formatter("percent")) 


# Define UI
ui <- fluidPage( 
  useShinyjs(),
  fluidRow(
    column( width = 6, div(style = "height: 500px;", left_bar)),
    column( width = 4, div(style = "height: 500px;", right_bar))
      ),
  fluidRow(
    column(width = 10, echarts4rOutput("bottom_bar", height = "300px"))
      )
)


# Define server logic
server <- function(input, output) {
  
  # Define a reactive expression to filter the data based on the selected state
  filtered_data <- reactive({
    if (!is.null(input$selected_state)) {
      data %>%
        filter(State == input$selected_state)
    } else {
      data
    }
  })
  
  # Update the bar chart when the user selects a state 
  output$bottom_bar <- renderEcharts4r({
    
    bar_title <- ifelse(is.null(input$selected_state), 'All', input$selected_state)
    
    bar_data <- filtered_data() %>%
      group_by(Product) %>% 
      summarise(Profit = sum(Profit), Target = sum(TargetProfit)) %>%
      mutate(color = ifelse(Profit > Target, '#90cee3', '#ffbb78')) %>%
      arrange(desc(Profit))
    
    bottom_bar <- bar_data %>%
      e_charts(Product) %>%
      e_bar(Profit) %>%
      e_title(text = paste0("Profit vs. Target Profit by Product in ", bar_title)) %>%
      e_add_nested("itemStyle", color) %>%
      e_legend(show = FALSE) %>%
      e_tooltip(trigger = "axis") %>%
      e_y_axis(name = "Profit",
               nameLocation = "center",
               nameGap = 50,
               axisLabel = list(
                 formatter = htmlwidgets::JS(
                   "function(value, index) {
                  return (value / 1000) + 'k';
                }"
                 )
               )) %>%
      e_error_bar(Target, Target, name = "Target")
    
    bottom_bar
  })
  
}


# Run the application
shinyApp(ui = ui, server = server)

```

# Result

![R08](https://user-images.githubusercontent.com/79496040/226181116-7076e642-82d2-45aa-b372-624b402f2270.gif)

