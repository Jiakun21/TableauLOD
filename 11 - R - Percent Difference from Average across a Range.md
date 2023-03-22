# Percent Difference from Average across a Range

```R
library(dplyr)
library(shiny)
library(plotly)
library(ggplot2)
library(lubridate)
library(forcats)
library(scales)

data <- read.csv("lod11.csv", header = TRUE, sep = ",")

data <- data %>%
  mutate(Date = as.Date(Date))


# Define UI
ui <- fluidPage(
  titlePanel("Percent Difference from Average across a Range"),
  sidebarLayout(
    sidebarPanel(
      selectInput("ticker", "Ticker:", choices = unique(data$Ticker)),
      dateRangeInput("dates", "Reference date range slider:", start = min(data$Date), end = max(data$Date))
    ),
    mainPanel(
      plotlyOutput("plots")
    )
  )
)

# Define server
server <- function(input, output) {
  
  # Create reactive data based on selected ticker and date range
  filtered <- reactive({
    data %>% 
      filter(Date >= input$dates[1] & Date <= input$dates[2] & Ticker == input$ticker)
  })
  
  # Area Plot
  AreaPlot <- reactive({
    Avg_Close <- mean(filtered()$Close)
    
    data %>%
      filter(Ticker == input$ticker) %>%
      mutate(Price_Diff = (Close - Avg_Close)/Avg_Close)
  })
  
  # Line Plot
  LinePlot <- reactive({
    data %>%
      filter(Ticker == input$ticker)
  })
  
  
  # Create area plot based on reactive data
  # Area plot cannot display the tool tips correctly. Line plot does not have this issue.
  area_plot <- reactive({
    ggplot(AreaPlot(), aes(x = Date, y = Price_Diff)) + 
      geom_area(fill = "#17becf", alpha = 0.7) +
      geom_line(color = "red", aes(x = input$dates[1], group = 1), linetype = "dashed") +
      geom_line(color = "red", aes(x = input$dates[2], group = 1), linetype = "dashed") +
      scale_y_continuous(labels = percent_format())

  })
  
  # Create line plot based on reactive data
  line_plot <- reactive({
    ggplot(LinePlot(), aes(x = Date, y = Close)) + 
      geom_line(color = "black") +
      geom_line(color = "gray", aes(y = round(mean(filtered()$Close),2))) +
      geom_line(color = "red", aes(x = input$dates[1], group = 1), linetype = "dashed") +
      geom_line(color = "red", aes(x = input$dates[2], group = 1), linetype = "dashed") +
      labs(x = "Date") 
  })
  
  # Combine the plots with shared x-axis using subplot function
  output$plots <- renderPlotly({
    subplot(
      ggplotly(area_plot(), width = 800, height = 600) %>% 
        layout(showlegend = FALSE),
      ggplotly(line_plot(), width = 800, height = 600) %>% 
        layout(showlegend = FALSE),
      nrows = 2,  
      shareX = TRUE
    ) %>%
     layout(yaxis = list(title = "Pct Change from Average across a Range", 
                        titlefont = list(size = 14)),
           yaxis2 = list(title = "Adj. Close", 
                         titlefont = list(size = 14))
           )
  })
  
}

# Run the app
shinyApp(ui, server)
         
```

# Result

![R11](https://user-images.githubusercontent.com/79496040/226950551-879da63f-08a5-40c7-8e37-3727d446345f.gif)

# Comment
Tooltips cannot be displayed correctly in the area plot. Line plot does not have this issue. </br>
Will try to plot using echarts4r instead of ggplot2 and plotly

