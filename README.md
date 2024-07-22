# AggregateSeurat

# Creating a Shiny App that will allow a use to input a Seurat Object, 
# select the headers from the Seurat Object that they want to see,
# then Aggregate Expression, display as a table, and download

# Installing necessary packages
library(shiny)
library(Seurat)
options(shiny.maxRequestSize = 10000*1024^2)


# writing ui 
ui <- fluidPage(
  titlePanel("Upload and Pseudobulk Seurat Object"), 
  sidebarLayout(
      sidebarPanel(
        textInput("projectName", "Project Name"),
        fileInput("seuratobj", "Choose Seurat Object",
                  accept = c(".h5seurat")),
        checkboxInput("headers", "File has headers", TRUE),
        
        selectizeInput("headers","Select Columns to Display:", choices = colnames(seurat_obj@meta.data) , multiple = TRUE, 
                       options = list(placeholder = 'Select headers', maxItems = NULL)),
        actionButton("uploadButton", "Upload")
      ),
  mainPanel(
    tabsetPanel(
      tabPanel("Original Seurat Object", tableOutput("contents")),
      tabPanel("Pseudobulked Seurat Object", tableOutput("pseudobulked"))
    )
  )
  )
)


# writing server function

server <- function(input, output, session) {
  seurat_obj <- reactive({
    req(input$uploadButton)
    inFile <- input$seuratobj
    if (is.null(inFile))
      return(NULL)
    seurat_obj <- LoadH5Seurat(inFile$datapath)
    return(seurat_obj)
  })
  output$contents <- renderTable({
    seurat <- seurat_obj()
    req(seurat)
    head(seurat@meta.data, n = 10)
  })
}

# Run the application 
shinyApp(ui = ui, server = server)
