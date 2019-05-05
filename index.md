---
title       : Assignment for Shiny App
subtitle    : Using Boston data as an example
author      : 
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## Correlation for Housing Price in Shiny App

1. The shiny app will produce a scatter plot between variables (selected from panel bar) and our house price outcome, a linear correlation estimator will be presented, and with an option for plotting a smooth curve.
2. Boston Data will be called in.  There were 506 samples, with 14 variables in Boston Dataset.
3. House price (variable: medv) is our interested outcome: medv – Median value of owner-occupied homes in $1000’s

--- .class #id 

## Variables in the Boston Data

1. crim  – per capita crime rate by town
2. zn    – proportion of residential land zoned for lots over 25,000 sq.ft
3. indus – proportion of non-retail business acres per town
4. chas  – Charles River dummy variable (1 if tract bounds river; else 0)
5. nox   – nitric oxides concentration (parts per 10 million)
6. rm    – average number of rooms per dwelling
7. age   – proportion of owner-occupied units built prior to 1940
8. dis   – weighted distances to five Boston employment centres
9. rad   – index of accessibility to radial highways
10. tax  – full-value property-tax rate per $10,000
11. ptratio    – pupil-teacher ratio by town
12. black      – 1000(Bk - 0.63)^2 where Bk is the proportion of blacks by town
13. lstat      – % lower status of the population

--- .class #id


## Part of my server.R file


```r
shinyServer(function(input, output) {
  output$distPlot <- renderPlot({
    # generate bins based on input$bins from ui.R
    x <- Boston[,input$xcol], y <- Boston$medv ### see median value of the homes
    par(mar=c(4,4,1,1))
    plot(x, y, pch = 19, col = 'black',
         xlab = input$xcol,
         ylab = "medv")
    text(quantile(x, probs = 0.8, na.rm=T), quantile(y, probs = 0.95, na.rm=T),
         paste("Corr = ", round(cor(x,y),2), sep=""), font = 4, col = "firebrick", cex=1.4, pos = 4)
    # Display only if smoother is checked. the line code mimic from R studio example
    if(input$smoother){
            smooth_curve <- lowess(x = x, y = y, f = input$f)
            lines(smooth_curve, col = "#E6553A", lwd = 3)
    } ### end if smoother })  })
```

--- .class #id 


## Part of my ui.R file


```r
shinyUI(fluidPage(
  titlePanel("Boston Housing Data correlations"), # Application title
  sidebarLayout(
    sidebarPanel(
       selectInput('xcol', 'X Variable', choices=colnames(Boston)),
       # Select whether to overlay smooth trend line
       checkboxInput(inputId = "smoother", label = strong("Overlay smooth trend line"), value = FALSE),
       # Display only if the smoother is checked. 
       conditionalPanel(condition = "input.smoother == true",
                        sliderInput(inputId = "f", label = "Smoother span:",
                                    min = 0.01, max = 1, value = 0.67, step = 0.01,
                                    animate = animationOptions(interval = 100)),
                        HTML("Higher values give more smoothness."))
    ),
    # Show a plot of the generated distribution
    mainPanel(
       plotOutput("distPlot") ) ) ))
```

