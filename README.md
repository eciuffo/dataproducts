# dataproducts

Stock Quote Decomposition

   This application was developed as a Data Products Class project as part of
the Data science Especialization of the Coursera/John Hopkins University program.

   The product focus on the use of the Shiny R package and implements the Quantmod R package, used here to decompose the three distinct components of a stock quote based on data downloaded from the source (eg. yahoo | google), selected from a drop box and covering a period of time selected by the Begin and End date chosen by the display calendars shown in the parameters session of the screen.
   
   The cyclical component is based on a frequency measured in months and also selected from the parameter session of the screen. The default value 12 stands for the usual one calendar year. Thee time window covered must allow for at least two consecutive cycles, otherwise the application will throw an error message.
   
   The stock simbol specified in the parameter session of the screen must be a valid stock quote as listed on "http://www.yahoo.com/lookup". No special validation pop up screen will show up and only an basic error message will appear as a result.
