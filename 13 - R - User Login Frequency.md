# User Login Frequency

```R
library(dplyr)
library(lubridate)
library(shiny)
library(echarts4r)
library(shinyjs)
library(htmlwidgets)

data <- read.csv("lod13.csv", header = TRUE, sep = "\t")

data <- data %>%
  mutate(LoginDate = as.Date(LoginDate)) %>%
  group_by(Market, UserID) %>%
  summarise(FirstLogin = min(LoginDate),
            LastLogin = max(LoginDate),
            TotalLogins = n_distinct(LoginDate)) %>%
  # interval(FirstLogin, LastLogin) %/% months(1)  
  mutate(TotalMonths = (year(LastLogin) - year(FirstLogin)) * 12 + (month(LastLogin) - month(FirstLogin)),
         LoginFreq = TotalMonths/TotalLogins,
         RndLoginFreq = ifelse(LoginFreq - floor(LoginFreq) >= 0.5, floor(LoginFreq) + 1, floor(LoginFreq))) 

df1 <- data %>%
  group_by(Market) %>%
  summarise(TotalUsers = n_distinct(UserID),
            AvgLoginFreq = round(mean(LoginFreq),3))

df2 <- data %>%
  group_by(Market, RndLoginFreq) %>%
  summarise(Users = n_distinct(UserID))

res <- left_join(df2, df1, by = "Market") %>%
  mutate(Pct = Users/TotalUsers,
         Diff = RndLoginFreq - AvgLoginFreq) %>%
  select(-c(3,4))

# Define UI
ui <- fluidPage(
  titlePanel("User Login Frequency"),  
  selectInput(inputId = "market",
              label = "Select market:",
              choices = unique(res$Market)), 
  echarts4rOutput("bar_chart")
)


# Define server logic
server <- function(input, output) {
  
  # Define a reactive expression to filter the data based on the selected market
  filtered_data <- reactive({
      res %>%
        filter(Market == input$market)
  })
  
  # Update the bar chart when the user selects a market 
  output$bar_chart <- renderEcharts4r({

    bar_chart <- filtered_data() %>%
      e_charts(RndLoginFreq) %>%
      e_bar(Pct) %>%
      e_visual_map(Diff,
                   dimension = 0,
                   orient = "horizontal",
                   top = 10,
                   left = 600,
                   inRange = list(color = c('#e16a31', '#fb9a57', '#afd3de','#8ecbe4', '#4e6789'))) %>%
      e_legend(show = FALSE) %>%
      e_tooltip(
                formatter = htmlwidgets::JS("
                              function(params) {
                                var content = '';
                                var value = (params.value[1]*100).toFixed(2);
                                content += '<b>' + value + '%</b> of users log in once every <b>' + params.value[0] + '</b> months.';
                                return content;
                              }
            ")) %>%
      e_x_axis(name = "Login Frequency", 
               nameLocation = "center",
               nameGap = 40,
               splitLine = list(show = FALSE)) %>%
      e_y_axis(name = "% of Total Distinct Users",
               nameLocation = "center",
               nameGap = 40,
               offset = 15,
               axisLine = list(onZero = FALSE),
               splitLine = list(show = FALSE),
               formatter = e_axis_formatter("percent")) %>%
      e_mark_line(data = list(xAxis = min(filtered_data()$AvgLoginFreq)), 
                  lineStyle = list(color = "black")) 
    
    bar_chart
  })  
}


# Run the application
shinyApp(ui = ui, server = server)

```

# Result

![R13](https://user-images.githubusercontent.com/79496040/227735432-cbf4558d-5fc5-4f60-8367-a608469a6e3e.gif)

