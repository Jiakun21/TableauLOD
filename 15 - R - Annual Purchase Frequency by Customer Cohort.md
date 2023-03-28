# Annual Purchase Frequency by Customer Cohort

```R
library(dplyr)
library(lubridate)
library(shiny)
library(echarts4r)
library(shinyjs)
library(htmlwidgets)

data <- read.csv("lod15.csv", header = TRUE, sep = "\t")

# Annual Purchase Frequency
data1 <- data %>%
  mutate(OrderDate = as.Date(OrderDate),
         OrderYear = year(OrderDate)) %>%
  group_by(CustomerID, OrderYear) %>%
  summarise(Freq = n_distinct(OrderID), .groups = 'drop')

# Customer Cohort
data2 <- data %>%
  mutate(OrderDate = as.Date(OrderDate)) %>%
  group_by(CustomerID) %>%
  summarise(CohortYear = year(min(OrderDate)), .groups = 'drop')

data3 <- data2 %>%
  group_by(CohortYear) %>%
  summarise(CohortCust = n())

res <- left_join(data1, data2, by = "CustomerID") %>%
  group_by(OrderYear, CohortYear, Freq) %>%
  summarize(NumCust = n_distinct(CustomerID)) %>%
  arrange(OrderYear, CohortYear, desc(Freq)) %>%
  mutate(CumNumCust = cumsum(NumCust)) %>%
  ungroup() %>%
  left_join(data3, by = "CohortYear") %>%
  mutate(Pct = CumNumCust/CohortCust) %>%
  select(-c(4:6))


# Define UI
ui <- fluidPage(
  titlePanel("Annual purchase frequency by customer cohort"),
  
  # Select input for order year
  selectInput(inputId = "orderyear",
              label = "Select Year:",
              choices = unique(res$OrderYear)),
  
  fluidRow(
    column(width = 8, echarts4rOutput("line_chart", height = "500px"))
  )

)


# Define server logic
server <- function(input, output) {
  
  # Define a reactive expression to filter the data based on the selected orderyear
  filtered_data <- reactive({
      res %>%
        filter(OrderYear == input$orderyear)
  })
  
  
  # Update the bar chart when the user selects a orderyear 
  output$line_chart <- renderEcharts4r({

    orderyear <- input$orderyear
    
    line_chart <- filtered_data() %>%
      group_by(CohortYear) %>%
      e_charts(Freq) %>%
      e_line(Pct) %>%
      e_color(c("#26456e","#1c73b1","#67add4","#b4d4da")) %>%
      e_tooltip(
                formatter = JS(paste0("
                              function(params) {
                                var content = 'In <b>", orderyear, "</b>, ';
                                var value = (params.value[1]*100).toFixed(0);
                                content += '<b>' + value + '%</b> of customers from the <b>' + params.seriesName + '</b> cohort purchased at least <b>' + params.value[0] + '</b> time.';
                                return content;
                              }
            "))) %>%
      e_x_axis(name = "Number of purchase orders per year", 
               nameLocation = "center",
               nameGap = 40,
               interval = 1,
               splitLine = list(show = FALSE)) %>%
      e_y_axis(name = "% of Cohort that Purchased at least N Times",
               nameLocation = "center",
               nameGap = 40,
               splitLine = list(show = FALSE),
               formatter = e_axis_formatter("percent")) 
    
    line_chart
  })
  
}


# Run the application
shinyApp(ui = ui, server = server)

```

# Result

![R15](https://user-images.githubusercontent.com/79496040/228266707-90361034-f168-47ff-8117-6dd504ea9766.gif)

