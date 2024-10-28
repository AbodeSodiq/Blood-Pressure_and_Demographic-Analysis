# Hypertension-rate
## Objectives
- Identify the rate of hypertension among staff.
- Examine clinic visitation trends by month and job level.
- Evaluate the impact of age on hypertension prevalence among employees.
- Develop recommendations for workplace health interventions.

## Milestone
Click here for the data planning chart.

## About Data
The data was retrieved from the company in-plant clinic database for the analysis. 

## Data Cleaning 
#### Load Library
````
library(readxl) 
library(dplyr)
````
````
con_file_path <- "CLINIC CONSULTATION 2.xlsx"
````
````
con_sheet_names <- excel_sheets(file_path)
````
`````
con_standardize_data <- function(sheet) {
  data <- read_excel(con_file_path, sheet = sheet)
  data <- data %>% mutate(across(everything(), as.character))
  return(data)
}
`````
````
con_all_data <- lapply(con_sheet_names, standardize_data) %>% bind_rows()
````
````
view(con_all_data)
````
#### View data present in unusual column 
````
view(check_column_data <- na.omit(con_all_data$...3))
````
#### View the filtered data 
````
con_ clean_data$...3
````
#### Move from one column to another
Count NA in a colum before moving
````
con_na_count <- sum(is.na(con_all_data$`PATIENT NAME`))
`````
Move from one column to the other
````
cleaned_con_all_data <- con_all_data %>%
    mutate(`PATIENT NAME` = ifelse(is.na(`PATIENT NAME`) | `PATIENT NAME` == "", `...3`, `PATIENT NAME`))
Move with removing the source
all_data <- all_data %>% mutate(Names = ifelse(is.na(Names) | Names == "", `...3`, Names)) %>% select(-`...3`)
````
Count NA in a colum to confirm 
````
con_na_count <- sum(is.na(con_all_data$`PATIENT NAME`))
````
#### Create separate tabe for the analysis
````
con_analysis_data <- move_con_all_data %>%
+    select(AGE, `B/P`, WEIGHT, LEVEL,)
````
#### Remove roll in which BP is NA
````
cleaned_con_analysis_data <- con_analysis_data %>%
    mutate(across(everything(), ~ gsub("\\s+", "", as.character(.)))) %>%
    filter(!is.na(`B/P`))
````
#### Clean LEVEL column
````
Single letter change such as T and t 
cleaned_con_analysis_data <- cleaned_con_analysis_data %>%
    mutate(LEVEL = ifelse(grepl("[Tt]", LEVEL), "T.P", LEVEL))
````
Further cleaning of LEVEL column 
`````
cleaned_con_analysis_data <- cleaned_con_analysis_data %>%
    mutate(LEVEL = gsub("^(S.s|S/S|s/S|ss|S/s|S\\s|c.s.o|s/s|Sales)$", "S.S", LEVEL))
`````

