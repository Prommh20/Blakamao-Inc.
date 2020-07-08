# Blakamao-Inc.
My First Website
Greetings ! I've used R Markdown to create a HTML document with tabbed sections and each tab should include one Leaflet map. When I run the code, the Leaflet maps only appear in the first two tabs of the HTML document and do not appear in any of the subsequent tabs. I've posted on stackoverflow and the RStudio Community and was directed here.

Here's a quick summary of what I've tried:

    I have tried the RStudio Viewer, Chrome, and Firefox, but all have the same results. When I open the browser console, there is an error that reads, "Error: Set map center and zoom first." However, I have set map center and zoom using setView() in Leaflet.

    When I run the code chunks independently in the Markdown file or in the R console, they work perfectly in generating the desired maps.

    When I remove the tabbed sections from the Markdown file, all the Leaflet maps appear under their respective headings.

    The order of the tabs does not matter, maps always appear on the first two tabs and do not appear on subsequent tabs (i.e. In the example, if you move east_map chunk to the beginning, there will be no map for cent_state).

    I have intentionally added the full set of code to each code chunk and renamed each filtered iteration to try to ensure I wasn't missing pulling something from the global environment.

    I have also tried using leafletOutput() and renderLeaflet(), but to no avail. But I don't think this is relevant as it applies more to Shiny integration.

    I updated R (3.6.0), RStudio (1.2.1335), pandoc (2.7.2), and all relevant packages.

    It was suggested my issue may be similar to this one.

I have scoured StackOverview, but am absolutely out of ideas and would appreciate any thoughts or guidance. In my document, I have 10 tabs, but for brevity, I have created an example below with just 3 tabs that reproduces my problem. (Also, I have cut away a lot of code from my full Markdown file to make this as straight-forward as possible, so apologies if there are some lingering packages, etc. that aren't immediately relevant.)

Thanks in advance!

---
title: "REPRODUCIBLE EXAMPLE <br> April 2019"
always_allow_html: yes
output:
  html_document:
    df_print: paged
---

# {.tabset .tabset-fade}

## TAB 1

### HEADING 1

```{r natl_map, echo = FALSE, message=FALSE, warning=FALSE, comment=NA, out.width='100%'}
setwd()
library(tidyverse)
library(leaflet)
library(leaflet.extras)
library(htmltools)
library(htmlwidgets)
library(shiny)
library(rgdal)

dat <- tibble(
  state = c("lak", "cent", "east", "east"),
  org = c("abc", "cbs", "fox", "abc"),
  item = c("pump 1", "pump 2", "pump 3", "pump 4"),
  status = c("terrible", "poor", "good", "excellent"),
  lat = c(6.87239, 4.01313, 5.00959, 4.77239),
  lon = c(29.57524, 30.56462, 32.39547, 	33.59156)
)

dat$status <- factor(dat$status, levels = c("terrible", "poor", "good", "excellent"))

#Set color pallette for by status
pal <- colorFactor(palette = c("#FF0000", "yellow", "#2cb42c", "blue"),
                   levels = levels(dat$status))

#Create factor layers by facility_type
abc_data <- dat %>%
  filter(org == "abc")

cbs_data <- dat %>%
  filter(org == "cbs")

fox_data <- dat %>%
  filter(org == "fox")

ssd_map <- leaflet() %>%
  addProviderTiles("OpenStreetMap.BlackAndWhite") %>%
  setView(lng = 30.2189853, lat = 7.1751893, zoom = 7) %>%
  setMaxBounds(
    lng1 = 22.625227,
    lat1 = 1.422041, 
    lng2 = 36.978083,
    lat2 = 13.528717 
  )

natl_map <- ssd_map %>% 
  clearMarkers() %>%
  addCircleMarkers(data	=	abc_data,
                   color = ~pal(status),
                   radius	=	2,
                   group = "abc") %>%
  addCircleMarkers(data	=	cbs_data,
                   color = ~pal(status),
                   radius	=	2,
                   group = "cbs") %>%
  addCircleMarkers(data	=	fox_data,
                   color = ~pal(status),
                   radius	=	2,
                   group = "fox") %>%
  addLayersControl(overlayGroups = c("abc",
                                     "cbs",
                                     "fox")) %>%
  addLegend(position = "topright",
            pal = pal,
            title = "Status",
            values = dat$status) %>%
  addResetMapButton()

natl_map
```

## TAB 2

### HEADING 2

```{r cent_map, echo = FALSE, message=FALSE, warning=FALSE, comment=NA, out.width='100%'}
setwd()
library(tidyverse)
library(leaflet)
library(leaflet.extras)
library(htmltools)
library(htmlwidgets)
library(shiny)
library(rgdal)

dat <- tibble(
  state = c("lak", "cent", "east", "east"),
  org = c("abc", "cbs", "fox", "abc"),
  item = c("pump 1", "pump 2", "pump 3", "pump 4"),
  status = c("terrible", "poor", "good", "excellent"),
  lat = c(6.87239, 4.01313, 5.00959, 4.77239),
  lon = c(29.57524, 30.56462, 32.39547, 	33.59156)
)

dat$status <- factor(dat$status, levels = c("terrible", "poor", "good", "excellent"))

#Set color pallette for by status
pal <- colorFactor(palette = c("#FF0000", "yellow", "#2cb42c", "blue"),
                   levels = levels(dat$status))

cent_dat <- dat %>%
  filter(state == "cent")

#Create factor layers by facility_type
cent_abc_data <- cent_dat %>%
  filter(org == "abc")

cent_cbs_data <- cent_dat %>%
  filter(org == "cbs")

cent_fox_data <- cent_dat %>%
  filter(org == "fox")

cent_map <- leaflet() %>%
  addProviderTiles("OpenStreetMap.BlackAndWhite") %>%
  setView(lng = 31.3222933, lat = 4.734494, zoom = 8) %>%
  setMaxBounds(
    lng1 = 32.149583,
    lat1 = 6.259701, 
    lng2 = 29.753375,
    lat2 = 3.501536 
  )

cent_map <- cent_map %>% 
  clearMarkers() %>%
  addCircleMarkers(data	=	cent_abc_data,
                   color = ~pal(status),
                   radius	=	2,
                   group = "abc") %>%
  addCircleMarkers(data	=	cent_cbs_data,
                   color = ~pal(status),
                   radius	=	2,
                   group = "cbs") %>%
  addCircleMarkers(data	=	cent_fox_data,
                   color = ~pal(status),
                   radius	=	2,
                   group = "fox") %>%
  addLayersControl(overlayGroups = c("abc",
                                     "cbs",
                                     "fox")) %>%
  addLegend(position = "topright",
            pal = pal,
            title = "Status",
            values = dat$status) %>%
  addResetMapButton()

cent_map
```

## TAB 3

### HEADING 3

```{r east_map, echo = FALSE, message=FALSE, warning=FALSE, comment=NA, out.width='100%'}
setwd()
library(tidyverse)
library(leaflet)
library(leaflet.extras)
library(htmltools)
library(htmlwidgets)
library(shiny)
library(rgdal)

dat <- tibble(
  state = c("lak", "cent", "east", "east"),
  org = c("abc", "cbs", "fox", "abc"),
  item = c("pump 1", "pump 2", "pump 3", "pump 4"),
  status = c("terrible", "poor", "good", "excellent"),
  lat = c(6.87239, 4.01313, 5.00959, 4.77239),
  lon = c(29.57524, 30.56462, 32.39547, 	33.59156)
)

dat$status <- factor(dat$status, levels = c("terrible", "poor", "good", "excellent"))

#Set color pallette for by status
pal <- colorFactor(palette = c("#FF0000", "yellow", "#2cb42c", "blue"),
                   levels = levels(dat$status))

east_dat <- dat %>%
  filter(state == "east")

#Create factor layers by facility_type
east_abc_data <- east_dat %>%
  filter(org == "abc")

east_cbs_data <- east_dat %>%
  filter(org == "cbs")

east_fox_data <- east_dat %>%
  filter(org == "fox")

east_map <- leaflet() %>%
  addProviderTiles("OpenStreetMap.BlackAndWhite") %>%
  setView(lng = 33.731222, lat = 5.084033, zoom = 8) %>%
  setMaxBounds(
    lng1 = 35.964000,
    lat1 = 3.556817, 
    lng2 = 31.597152,
    lat2 = 5.906979  
  )

east_map <- east_map %>% 
  clearMarkers() %>%
  addCircleMarkers(data	=	east_abc_data,
                   color = ~pal(status),
                   radius	=	2,
                   group = "abc") %>%
  addCircleMarkers(data	=	east_cbs_data,
                   color = ~pal(status),
                   radius	=	2,
                   group = "cbs") %>%
  addCircleMarkers(data	=	east_fox_data,
                   color = ~pal(status),
                   radius	=	2,
                   group = "fox") %>%
  addLayersControl(overlayGroups = c("abc",
                                     "cbs",
                                     "fox")) %>%
  addLegend(position = "topright",
            pal = pal,
            title = "Status",
            values = dat$status) %>%
  addResetMapButton()

east_map
```
