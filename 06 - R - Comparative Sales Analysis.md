# Comparative Sales Analysis

```R
library(dplyr)
library(shiny)
library(plotly)
library(ggplot2)

data <- read.csv("LOD06.csv", header = TRUE, sep = ";")

# Define the data
Sales_per_Cat <- data %>%
                  group_by(Category) %>%
                  summarise(Sales = sum(Sales))

# Define the UI
ui <- fluidPage(
  titlePanel("Cohort Analysis"),
  
  # Select input for category
  selectInput(inputId = "category",
              label = "Category",
              choices = unique(Sales_per_Cat$Category)),
  
  fluidRow(
    column(width = 6, plotOutput("left_bar", width = "100%")),
    column(width = 6, plotOutput("right_bar", width = "100%"))
  )
)

# Define the server
server <- function(input, output) {
  
  # Filter the data based on the selected item
  selected_cat <- reactive({
    Sales_per_Cat %>% 
      filter(Category == input$category)
  })
  
  
  # Left Bar
  left_df <- reactive({
    sel_sales <- sum(selected_cat()$Sales)
    
    Sales_per_Cat %>% 
      mutate(Diff = Sales - sel_sales) %>%
      arrange(desc(Diff)) %>%
      mutate(Category = reorder(Category, Diff))
  })
  
  
  # Create the left bar chart
  output$left_bar <- renderPlot({
    ggplot(left_df(), aes(x = Diff, y = Category, fill = Diff)) +
      geom_bar(stat = "identity") +
      scale_x_continuous(labels = scales::comma) +
      scale_fill_gradient(low = "#d45b22", high = "#2b5c8a") +
      labs(x = "Difference from Selected", y = "") +
      # theme(legend.position = "top") +
      theme(panel.background = element_rect(fill = "white")) + 
      guides(fill = FALSE)
  }) 
    
  
  # Create the right bar chart
  output$right_bar <- renderPlot({
    
    # get selected category from dropdown list
    selected_category <- input$category 
    
    my_df <- left_df()
    my_df$selected <- ifelse(my_df$Category == selected_category, "Selected", "Not Selected")
    
    ggplot(my_df, 
           aes(x = Sales, y = Category, fill = selected)) +
      geom_bar(stat = "identity") +
      scale_x_continuous(labels = scales::comma) +
      labs(x = "Sales") +
      theme(panel.background = element_rect(fill = "white")) +
      theme(axis.line.y = element_blank(),
            axis.text.y = element_blank(),
            axis.ticks.y = element_blank(),
            axis.title.y = element_blank()) + 
      
      # set fill color for selected and non-selected bars
      scale_fill_manual(values = c("Selected" = "yellow", "Not Selected" = "gray")) +
      guides(fill = FALSE)    
  })
}

# Run the app
shinyApp(ui, server)
```

# Result

![R06](https://user-images.githubusercontent.com/79496040/223859701-7f82362a-d263-4e2c-b285-9e2b75d46bf1.gif)

# Comment

Will add tooltips feature
