---
title       : Pitch presentation of Tooth Growth Study App
subtitle    : 
author      : Sun Huiyuan
job         : 
framework   : io2012       # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## Objective

This is the pitch presentation of the app I created to show the study of The Effect of Vitamin C on Tooth Growth in Guinea Pigs. The datasets used is ToothGrowth data from datasets library. And in this presentation, I will enclose the code I made the app and the link to the app

--- 

## Functions of App

1. Available options are listed on the left pannel and main plots and info will show on the right pannel
2. You can choose whether to show Regression Line, Confidence Interval or Regression line formular
3. Selection of types of supplyments are also available on the left pannel

---

## Code

```r
library(shiny)
library(datasets)
library(ggplot2)
library(plotly)
ui<-fluidPage(
  
  # Application title
  titlePanel("The Effect of Vitamin C on Tooth Growth in Guinea Pigs"),
  
  # Sidebar with a slider input for number of bins 
  sidebarLayout(
    sidebarPanel(
      checkboxInput(inputId = "RL",
                    label = strong("Show Regression Line"),
                    value = FALSE),
      checkboxInput(inputId = "CI",
                    label = strong("Show Confidence Interval"),
                    value = FALSE),
      checkboxInput(inputId = "FM",
                    label = strong("Show Regression Line Formular"),
                    value = FALSE),
      hr(),
   
      checkboxGroupInput(inputId = "SP","Supplyments:",
                    choices = c("VC"="VC","OJ"="OJ"),
                    selected = c("VC","OJ")
      )
    ),
    
    # Show a plot of the generated distribution
    mainPanel(
 
      plotlyOutput("Plot"),
      textOutput("text1"),  
      textOutput("text2") 
    )
  )
)
server<-function(input, output) {
   datatb<-data.frame(ToothGrowth)
 
  output$Plot <- renderPlotly({
    filterdata<-data.frame(len=numeric(),supp=factor(levels=c("OJ","VC")),dose=numeric())
    if("VC" %in% input$SP){
      filterdata<-rbind(filterdata,datatb[datatb$supp=="VC",])
    }
    if("OJ" %in% input$SP){
      filterdata<-rbind(filterdata,datatb[datatb$supp=="OJ",])
    }
    
    p<-plot_ly(data=filterdata,x=~dose,y=~len)%>%
      add_markers(color=~supp,colors=c("blue","red"))%>%
      layout(title="Tooth Growth Chart",
             yaxis=list(title="Dose level",font=t),
             xaxis=list(title="Tooth Length",font=t))
    
    if(input$RL){
      if("VC" %in% input$SP){
        fitVC<-lm(len~dose,datatb[datatb$supp=="VC",])
        predVC<-predict(fitVC,datatb[datatb$supp=="VC",],interval="confidence")
        predVC<-data.frame(predVC)
        p<-add_lines(p,x=~datatb[datatb$supp=="VC",3],y=~predVC$fit,color=~as.factor("VC"),
                      name="VC supplyment Fitted line",showlegend=FALSE)
      }
      if("OJ" %in% input$SP){
        fitOJ<-lm(len~dose,datatb[datatb$supp=="OJ",])
        predOJ<-predict(fitOJ,datatb[datatb$supp=="OJ",],interval="confidence")
        predOJ<-data.frame(predOJ)
        p<-add_lines(p,x=~datatb[datatb$supp=="OJ",3],y=~predOJ$fit,color=~as.factor("OJ"),
                      name="OJ supplyment Fitted line",showlegend=FALSE)
      }
    }
    
    if(input$CI){
      if("VC" %in% input$SP){
        fitVC<-lm(len~dose,datatb[datatb$supp=="VC",])
        predVC<-predict(fitVC,datatb[datatb$supp=="VC",],interval="confidence")
        predVC<-data.frame(predVC)
        p<-add_ribbons(p,x=~datatb[datatb$supp=="VC",3],ymin=predVC$lwr,
                       ymax = predVC$upr,opacity=0.4,inherit = FALSE,
                       color=~as.factor("VC"),showlegend=FALSE)
      }
      if("OJ" %in% input$SP){
        fitOJ<-lm(len~dose,datatb[datatb$supp=="OJ",])
        predOJ<-predict(fitOJ,datatb[datatb$supp=="OJ",],interval="confidence")
        predOJ<-data.frame(predOJ)
        p<-add_ribbons(p,x=~datatb[datatb$supp=="OJ",3],ymin=predOJ$lwr,
                       ymax = predOJ$upr,opacity=0.4,inherit=FALSE,
                       color=~as.factor("OJ"),showlegend=FALSE)
      }
    }
    p
    
  })
  output$text1<-renderText({
    if(input$FM){
      if("VC" %in% input$SP){
        fitVC<-lm(len~dose,datatb[datatb$supp=="VC",])
        paste0("Supplyment VC fitted line formular is:"
                  ,round(fitVC$coefficients[1],2),"+",round(fitVC$coefficients[2],2),
                  "*Dose level")
      }
    }
  })
  output$text2<-renderText({
    if(input$FM){

      if("OJ" %in% input$SP){
        fitOJ<-lm(len~dose,datatb[datatb$supp=="OJ",])
        paste0("Supplyment OJ fitted line formular is:"
                          ,round(fitOJ$coefficients[1],2),"+",round(fitOJ$coefficients[2],2),
                          "*Dose level")
      }
      
    }
    
  })
  
  
}
shinyApp(ui = ui, server = server)
```

```
## Error in appshot.shiny.appobj(structure(list(httpHandler = function (req) : appshot of Shiny app objects is not yet supported.
```


---

## Links and Thanks

Links: https://sunclb.shinyapps.io/ToothGrowthStudy
##          Thanks
