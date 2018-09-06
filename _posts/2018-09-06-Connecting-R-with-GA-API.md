---
title: Connecting R with Google Analytics via API
description: How to connect to the API of Google Analytics and return results in the shape of a R dataframe
---

Extract data directly from Google Analytics using R is possible via the API. This is the process to authenticateand create a simple
query returning a R DataFrame:

1. Packages needed: To work with the Google Analytics API you need two packages.
   ```R
   library(httpuv)             # Web socket service package
   library(RGoogleAnalytics)   # Conexion R con GA
   ```
2. Authenticate into the right account. Here there are two possibilities, either you have acces to Google Cloud Platform and the
   or you don't, in each case these are the steps:
   * If you **don't** have access: You can connect directly to GA. Using this code a web browser will prompt asking for your email and
     password-
     Once you are authenticated into GA you can check all your accounts and get the viewId for the one you need
     ```R
     ga_auth()                   # Opens browser to authenticate
     my_accounts <- ga_account_list()
     View(my_accounts)           # Search for the account and copy the viewId
     ```
   * If you **do** have access to GCP: Access the APIs and Oath service and get the credential for the Client ID for native application.
     You need both the clientId and the clientSecret.  Then you can create a token to authenticate
     ```R
     client.id     <- <clienId>
     client.secret <- <secret>

     #creacion y llamada del token

     token<-Auth(client.id,client.secret)
     invisible(GetProfiles(token))
     ValidateToken(token)
     ```
     You also need to copy the viewId with this process
3. Running a query is also different if you have a token (acces to GCP or not):
   * If you **don't** have a token: The query can be run this way, returning a typical R dataframe
     ```R
     viewId     <- "XXXXXXXXX"
     date       <- c("YYYY-MM-DD", "YYYY-MM-DD")
     metrics    <- "ga:metric_name"
     dimensions <- c("ga:dimensions_name", ...)

     # Query
     df1 <- google_analytics(viewId, 
                             date_range = date, 
                             metrics=metrics, 
                             dimensions=dimensions)
     ```
   * If you **do** have a token: You need to use the GetReportData function to run your query, like this
     ```R
     viewId <- "ga:XXXXXXXXX"
     
     fechaInicio <- "YYYY-MM-DD"
     fechaFin    <- "YYYY-MM-DD"
     
     query <- Init(start.date  = fechaInicio,
                   end.date    = fechaFin,
                   metrics     = "ga:metrics_name, ...",
                   dimensions  = "ga:dimension_name, ...",
                   max.results = n,
                   table.id    = viewId)

     query <- QueryBuilder(query)
     data  <- GetReportData(query, token[, split_daywise = T, paginate_query = T, ...])
     ```
