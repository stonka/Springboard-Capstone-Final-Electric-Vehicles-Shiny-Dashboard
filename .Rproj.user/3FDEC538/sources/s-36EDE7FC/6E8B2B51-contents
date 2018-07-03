library(shinydashboard)
library(shiny)
library(dplyr)
library(tidyr)
library(readr)
library(leaflet)
library(lubridate)
library(rgeolocate)
library(rworldmap)
library(plyr)
library(ggmap)
library(ggplot2)
library(rgdal)
library(readxl)
library(stringr)
library(httr)
library(jsonlite)
library(RCurl)
library(XML)
library(rjson)
library(GGally)
library(scales)

#rm(list=ls())

df <- read_csv("ecs_api_df.csv")

df$UsageTypeID[is.na(df$UsageTypeID)] <- "0"
df <- mutate(df, UsageTypeID = recode(UsageTypeID,
                                      "1"="Private(For staff, Visitors, Customers)",
                                      "2"="Private (Restricted Access)",
                                      "3"="Privately Owned (Notice Required)", 
                                      "4"="Public",
                                      "5"="Public (Membership Required)",
                                      "6"="Public (Notice Required)",
                                      "7"="Public (Pay At Location)")
)


df <- unite(df, Address, AddressInfoTitle:AddressInfoStateOrProvince, remove = FALSE, sep = ', ')
df <- type_convert(df)


ui <- dashboardPage(
  dashboardHeader(title = "EV Chargers"),
  dashboardSidebar(
    #sidebarMenuOutput("Time"),
    ## Sidebar content
    sidebarMenu(
      menuItem("Dashboard", tabName = "dashboard", icon = icon("dashboard")),
      #menuItem("Widgets", tabName = "widgets", icon = icon("th")),
      menuItem("Data", tabName = "data", icon = icon("table")),
      menuItem("Interactive Map", tabName = "map", icon = icon("map")),
      #menuItem("Source data - API", icon = icon("file-code-o"), 
      #href = "https://api.openchargemap.io/v2/poi/?output=json&maxresults=80000&compact=true&verbose=false"),
      menuItem("Source data - Web", icon = icon("file-code-o"), 
               href = "https://openchargemap.org/site"),
      checkboxInput('verified', 'Recently Verified Only'),
      dateRangeInput('created', 'Date Created',
                     start = '2010-01-01',
                     end = today(),
                     min = '2010-01-01',
                     max = today())
    )
  ),
  
  dashboardBody(
    
    tabItems(
      tabItem(tabName = "dashboard",
              h2("Electric Vehicles (EV) Charging Stations: An Overiew"),
              
            fluidRow(
              infoBox(
                title = "EV Charging Stations -- Total Submissions", uiOutput("infoBox1"), icon = icon("check-circle"), color = "purple"),
              
              infoBox(
                title = "EV Charging Stations -- Recently Verified Submissions (%)", uiOutput("infoBox2"), icon = icon("pie-chart"), color = "blue"),  
              
              infoBox(
                title = "Number of Participating Countries", uiOutput("infoBox3"), icon = icon("globe"), color = "green"), 
              
              box(
                title = "EV Charging Stations Worldwide",
                plotOutput("plot2")),
                
              box(
                title = "EV Charging Stations Submissions Over Time",
                plotOutput("plot3"))
                
              ),
              
            fluidRow(
              box(
                title = "EV Charging Stations Submissions per 'Charge Usage Type ID'",
                plotOutput("plot1")),
                    
              box(
                title = "EV Charging Stations per 'Country ID'",
                plotOutput("plot4"))
              )
      ),        
      
    tabItem(tabName = "data",
              h2("Data Table"),
              dataTableOutput('data')
      ),
    
    tabItem(tabName = "map",
              h2("Interactive Map"),
              leafletOutput('map'))
      )))
    



server <- function(input, output) {
  
  
  # This creates a new dataset based on selections on the left
  
  filter_data <- reactive({
    df %>%
      filter(IsRecentlyVerified == input$verified) %>%
      filter(DateCreated > input$created[1]) %>%
      filter(DateCreated < input$created[2]) 
  })
  
  
  output$plot1 <- renderPlot({
    
    
    g <- ggplot(filter_data()[which(filter_data()$UsageTypeID>0),], aes(UsageTypeID))
    g +
      geom_bar(aes(fill = UsageTypeID), use = "complete.obs") +
      labs(x="Charging Usage Type ID", y="EV Charging Stations Submissions") +
      theme(axis.text.x = element_blank())
      #theme(axis.text.x = element_text(angle = 15, hjust = 1))
    
  })
  

  output$plot2 <- renderPlot({
    
    newmap <- getMap()
    
    newmap <- getMap(resolution = "low", projection = NA)
    
    plot(newmap, xlim = range(filter_data()$AddressInfoLongitude), ylim = range(filter_data()$AddressInfoLatitude), asp = 1)
    
    points(filter_data()$AddressInfoLongitude, filter_data()$AddressInfoLatitude, col="green", cex=.3)
    
    
  })
    
    
  output$plot3 <- renderPlot({
    
    
      t <- count(filter_data()$DateCreated)    
      plot(t, 
          xlab="Year",
          ylab="EV Charging Stations Submissions")
  })
  
  output$plot4 <- renderPlot({
    
    ggplot(data = filter_data()) +
      geom_bar(mapping = aes(x = AddressInfoCountryID), fill="tomato2", use = "complete.obs") +
        labs(x="Country ID (ISO)", y="EV Charging Stations Submissions")
               
  })
  
  output$infoBox1 <- renderText({
    a <- nrow(filter_data())
    comma(a)
  
  })
  
  output$infoBox2 <- renderText({
    v <- sum(df$IsRecentlyVerified, na.rm=TRUE)
    a <- nrow(df)
    p <- v/a
    percent(p)
    
  })
  
  output$infoBox3 <- renderText({
    length(unique(filter_data()$AddressInfoCountryID))
    
  })
  
  output$map <- renderLeaflet({
    
    filter_data() %>%
      sample_n(1000) %>% # Taking a sample, because the full dataset is too much for the map
      leaflet() %>% 
      addTiles() %>%
      addMarkers(~AddressInfoLongitude,
                 ~AddressInfoLatitude,
                 popup = ~Address)
    
  })
  
  output$data <- renderDataTable({
    
    filter_data()
    
  })
  
}

shinyApp(ui, server)











