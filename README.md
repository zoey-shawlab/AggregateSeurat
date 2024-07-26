# AggregateSeurat

# Creating a Shiny App that will allow a use to input a Seurat Object, 
# select the headers from the Seurat Object that they want to see,
# then Aggregate Expression, display as a table, and download

# Installing necessary packages
library(shiny)
library(Seurat)
library(SeuratDisk)
library(DT)

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
      uiOutput("select_pseudobulk"),
      uiOutput("pseudobulk_button"),
      actionButton("uploadButton", "Upload")
    ),
    mainPanel(
      tabsetPanel(
        tabPanel("Original Seurat Object", dataTableOutput("contents")),
        tabPanel("Pseudobulked Seurat Object", dataTableOutput("pseudobulked"))
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
  
  # create select function for pseudobulk
  output$select_pseudobulk <- renderUI({
    seurat <- seurat_obj()
    req(seurat)  
    
    col_names <- colnames(seurat@meta.data)
    
    selectizeInput("selected_columns", "Select Columns to Pseudobulk", choices = col_names, multiple = TRUE)
  })
  
  # displaying table on original seurat object tab
  output$contents <- renderDataTable({
    seurat <- seurat_obj()
    req(seurat)
    head(seurat, n = 100)
  })
  
  
  # pseudobulk button 
  output$pseudobulk_button <- renderUI({
    selected_columns <- input$selected_columns
    if (length(selected_columns) > 0) {
      actionButton("pseudobulkButton", "Pseudobulk")
    }
  })
  
  pseudobulk_action <- reactiveVal(FALSE)
  
  observeEvent(input$pseudobulkButton, {
    pseudobulk_action(TRUE)
  })
  
  # pseudobulk table 
  output$pseudobulked <- renderDataTable({
    seurat <- seurat_obj()
    req(seurat)
    
    if (pseudobulk_action()) {
      selected_columns <- input$selected_columns
      
      if (length(selected_columns) > 0 && all(selected_columns %in% colnames(seurat@meta.data))) {
        
        aggregated_data <- AggregateExpression(seurat, group.by = selected_columns)
        
        as.data.frame(aggregated_data$RNA)
      } else {
  
        data.frame(Error = "Selected columns not found in metadata or none selected")
      }
    } else {
      NULL
    }
  })
}

# Run the application 
shinyApp(ui = ui, server = server)
