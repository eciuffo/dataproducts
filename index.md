---
title       : Devoloping Data Products
subtitle    : Class Project 
author      : Ewerton Ciuffo
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight,js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft,selfcontained}
knit        : slidify::knit2slides
--- 

## Stock Quote Decomposition

<div class="container">
<h5>This application was developed as a Data Products Class project as part of
the <b>Data science Specialization</b> of the <b>Coursera/John Hopkins University</b> program.
</h5><p>
<h5>The product focus on the use of the Shiny R package and implements the Quantmod R package, used here to decompose the three distinct components of a stock quote based on data downloaded from the source (eg. yahoo | google), selected from a drop box and covering a period of time selected by the Begin and End date chosen by the display calendars shown in the parameters session of the screen.
</h5><p>
<h5>The cyclical component is based on a frequency measured in months and also selected from the parameter session of the screen. The default value 12 stands for the usual one calendar year. The time window covered must allow for at least two consecutive cycles, otherwise the application will throw an error message.
</h5><p>   
<h5>The stock simbol specified in the parameter session of the screen must be a valid stock quote as listed on "http://www.yahoo.com/lookup". No special validation pop up screen will show up and only an basic error message will appear as a result.
</h5></div>

---

## The Input 
<div class="container">
<h5>This application will receive the following entries:</h5><p>
<h5>  1. Frequency - The lenght in months where the ciclycal pattern will be evaluated. 
                     The default value for this value is 12 months(1 year).</h5><p>
<h5>  2. Source    - The source of the data where the calculations will be based.
                    the default value will be yahoo. other options will show as a dropdown.</h5><p>
<h5>  3. The stock symbol - The stock sticker as defined on www.yahoo.com/definition
        No validations will be performed on this value. If it does not match a valid
        symmbol, an error message will be displayed. No special popup screen will be shown
        in order to alert the user.</h5><p>
<h5>  4. The Start date - the date from which the data will  be retrieved from the source.
                          It is vital that the length of time between Start ands Ends Date
                          allow for at least two cycles. By default this date is set to 
                          1/1/1980 and users will be able to open an input window with a 
                          calendar just by hovering over the field.</h5><p>
<h5>   5. The End date - the closing date for the retrieved perion. </h5>
</div>

--- .class #id 

## The Output

       The output for the requested information will be a sequence of four distinct
       graphics. The first one will show the observed values as listed in the Opening
       values for the stock market. 

       The Second graphic will depict the general trend of the quote. This trend will show
       the part of the value when the Ciclycal and Unexplained components are stripped from
       the observed value.

       The third and fourth graphics represent respectively, the ciclycal an the
       Unexplained component of the observed value.

       Please note that these graphics were drawn for educational purposes only and they
       should not be considered as an advise. Any use of the data exposed here is "at your
       own risk". 

--- .class #id 

## The UI component of Shiny


```r
library(shiny)

shinyUI(pageWithSidebar(
  headerPanel('Stock Quote Decomposition'),
  sidebarPanel(
    sliderInput(inputId="freq", label="Choose a trend frequency desired",value = 12, min = 2, max = 36),
    selectInput(inputId="mysrc",label="Choose a source:", choices = c("yahoo", "google"),selectize=FALSE),
    textInput('symbol','Stock Symbol','MSFT'),
    dateInput('ifrom', "Start Date", value = "1980-01-01", min = NULL, max = NULL, format = "yyyy-mm-dd", startview = "month", weekstart = 0, language = "en"),
    dateInput('ito', "End Date", value = "2015-11-22", min = NULL, max = NULL, format = "yyyy-mm-dd", startview = "month", weekstart = 0, language = "en")),
  mainPanel(
      plotOutput('decompositionPlot')
    )
  ))
```

--- .class #id

## The Server component of Shiny


```r
library(shiny)
library(xts)
```

```
## Loading required package: zoo
## 
## Attaching package: 'zoo'
## 
## The following objects are masked from 'package:base':
## 
##     as.Date, as.Date.numeric
```

```r
library(quantmod)
```

```
## Loading required package: TTR
## Version 0.4-0 included new data defaults. See ?getSymbols.
```

```r
shinyServer(function(input, output, session) {
  output$decompositionPlot <- renderImage({
    outfile <- tempfile(fileext='.png')
    
    from.dat <- as.Date(input$ifrom, format="%Y-%m-%d")
    to.dat   <- as.Date(input$ito, format="%Y-%m-%d")
    #mysrc.dat<-input.mysrc
    mysrc.dat<-input$mysrc
    myfreq   <-as.numeric(input$freq)
    windowl<-as.numeric(input$to.dat - input$from.dat)
     
    mySymb <-getSymbols(Symbols=c(input$symbol), src=mysrc.dat, from=from.dat, to=to.dat, auto.assign=FALSE)
    mySymbol <- to.monthly(mySymb)
    myOpen   <- Op(mySymbol)
    
    pricesTimeSeries <- ts(myOpen, frequency = 12)
    
    # Generate the PNG
    png(outfile, width=990, height=600)
    plot(decompose(pricesTimeSeries,type = c("additive")))
    dev.off()
    
    # Return a list containing the filename
    list(src = outfile,
         contentType = 'image/png',
         width = 900,
         height = 600,
         alt = "Wait image loading")
  }, deleteFile = TRUE)
  })
```

---
