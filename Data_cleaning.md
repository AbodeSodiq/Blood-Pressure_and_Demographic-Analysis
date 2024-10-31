## Data Cleaning 
#### Load Library
````
library(readxl) 
library(dplyr)
````
#### Create file path and function
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
#### Combine rows
````
con_all_data <- lapply(con_sheet_names, standardize_data) %>% bind_rows()
````
##### View
````
view(con_all_data)
`````
![All_consultation_data](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Consultation_all_data.png)

#### View data present in unusual column 

![Consultation_unusual_column](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Consultation_error(puls,..7).png)

![Consultation_unusual_column](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Consultation_error(..7...3).png)

````
view(check_column_data <- na.omit(con_all_data$...3))
````
#### View the filtered data 
````
con_ clean_data$...3
````
#### Move from one column to another

It was observed that the column "PULS" contain, "...7" contain prescribed drugs, and "...3" contain patient name. The data is then moved to the appropriate column and deleted. 

##### Count NA in a column before moving
````
con_na_count <- sum(is.na(con_all_data$`PATIENT NAME`))
`````
##### Move from one column to the other
````
cleaned_con_all_data <- con_all_data %>%
    mutate(`PATIENT NAME` = ifelse(is.na(`PATIENT NAME`) | `PATIENT NAME` == "", `...3`, `PATIENT NAME`))
Move with removing the source
all_data <- all_data %>% mutate(Names = ifelse(is.na(Names) | Names == "", `...3`, Names)) %>% select(-`...3`)
````

##### Count NA in a column to confirm 
````
con_na_count <- sum(is.na(con_all_data$`PATIENT NAME`))
````

#### Create a separate table for the analysis
````
con_analysis_data <- move_con_all_data %>%
+    select(AGE, `B/P`, WEIGHT, LEVEL,)
````
![Consultation_data](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Con_analysis_data1.png)

#### Remove roll in which BP is NA
````
cleaned_con_analysis_data <- con_analysis_data %>%
    mutate(across(everything(), ~ gsub("\\s+", "", as.character(.)))) %>%
    filter(!is.na(`B/P`))
````

![Consultation_data](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Cleaned_con_analysis1.png)

#### Clean LEVEL column
##### Check unique values in the "LEVEL" column to understand it.

![Unique_level_column](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Levels_error.png)

There are a lot of errors seen in this table. There should be only  Senior Staff (S.S), Junior Staff (J.S), and Third Party (T.P). 

````
cleaned_con_analysis_data <- cleaned_con_analysis_data %>%
    mutate(LEVEL = ifelse(grepl("[Tt]", LEVEL), "T.P", LEVEL))
````

##### Further cleaning of the LEVEL column 

`````
cleaned_con_analysis_data <- cleaned_con_analysis_data %>%
    mutate(LEVEL = gsub("^(S.s|S/S|s/S|ss|S/s|S\\s|c.s.o|s/s|Sales)$", "S.S", LEVEL))
`````

## Export data to Excel
The cleaned analysis data was exported to Excel for further cleaning and data visualization. 
It was observed that some information is not in the right column

![Consultation_data](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Consultation_data_misplaced.png)

#### All data moved to their appropriate column

![Consultation_data](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Consultation_misplaced_correction.png)

## Determine hypertensive patient

Systolic (The value before "/", and diastolic (the value after) were separated into different columns. TRUE is used to indicate systolic reading above 139, and diastolic above 89 while FALSE is used to indicate values below. 

![Consultation_data](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Hypertension_calculation1.png)

![Monthly_percentage](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Hypertension_calculation2.png)

##### The result confirmed with a highlight

![Monthly_percentage](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Hypertension_calculation_check.png)

### The percentage of each month was calculated

![Monthly_percentage](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Month_percentage&number.png)

### Clean age column

Age column was cleaned by removing space, and separated the first two figures which represent the age.

![Age_clean](https://github.com/AbodeSodiq/Hypertension-rate-/blob/main/Tables/Age_clean.png)
