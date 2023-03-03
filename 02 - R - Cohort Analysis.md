# Cohort Analysis

```R
library(dplyr)
library(lubridate)
library(shiny)
library(plotly)
library(ggplot2)
library(forcats)
library(scales)

data <- read.csv("lod02.csv", header = TRUE, sep = "\t")

data <- data %>% mutate(OrderYear = year(as.Date(OrderDate)))
Cohort <- data %>% group_by(CustomerID) %>% summarise(CohortYear = min(OrderYear))

# Define the data
data <- left_join(data, Cohort, by = "CustomerID")


# Define the UI
ui <- fluidPage(
  titlePanel("Cohort Analysis"),
  
  # Select input for market
  selectInput(inputId = "market",
              label = "Select market:",
              choices = unique(data$Market)),
  
  fluidRow(
    column(width = 6, plotOutput("stacked_bar", width = "100%")),
    column(width = 6, plotOutput("percent_stacked_bar", width = "100%"))
  )

)

# Define the server
server <- function(input, output) {
  
  # Filter the data based on the selected item
  filtered_df <- reactive({
    data %>% filter(Market == input$market)
  })
  
  # Sum the sales for different CohortYears
  summed_df <- reactive({
    filtered_df() %>% 
      group_by(OrderYear, CohortYear) %>% 
      summarise(Sales = sum(Sales), .groups = "drop")
  })
  
  # % of Total sales for different CohortYears
  pct_df <- reactive({
      filtered_df() %>%
      group_by(OrderYear, CohortYear) %>%
      summarise(Sales = sum(Sales), .groups = "drop") %>%
      group_by(OrderYear) %>% 
      mutate(Sales_pct = Sales / sum(Sales))
  })

  
  # Create the first stacked bar chart
  output$stacked_bar <- renderPlot({
    ggplot(summed_df(), aes(x = OrderYear, y = Sales, fill = fct_rev(as.factor(CohortYear)),
                            text = paste0("Year: ", OrderYear, "<br>",
                                          "Cohort Year: ", CohortYear, "<br>",
                                           "Sales: $", scales::comma(Sales)))) +
      geom_bar(stat = "identity") +
      scale_fill_manual(values = c("#26456e", "#1c5f9e", "#418dbb", "#7ec8e1"), name = "Cohort Year") +
      labs(x = "Order Year", y = "Sales") +
      guides(fill=FALSE) +
      scale_y_continuous(labels = unit_format(unit = "K", scale = 1e-3))
    
  })

  
  # Create the second percentage stacked bar chart
  output$percent_stacked_bar <- renderPlot({
    ggplot(pct_df(), aes(x = OrderYear, y = Sales_pct, fill = fct_rev(as.factor(CohortYear)))) +
      geom_bar(stat = "identity", position = "fill") +
      scale_fill_manual(values = c("#26456e", "#1c5f9e", "#418dbb", "#7ec8e1"), name = "Cohort Year") +
      labs(x = "Order Year", y = "% of Total Sales") +
      scale_y_continuous(labels = percent_format()) 

  })
  

}

# Run the app
shinyApp(ui, server)

```

# Result

![R02](https://user-images.githubusercontent.com/79496040/222766900-51f0a57d-bfb6-43d6-af7c-48e25a671ada.gif)

# Comment

Will add tooltips feature
