# load CDM data into duckdb

library(CommonDataModel)
library(DBI)
library(dplyr)

# The conditin_occurrence table has some bad values that need to be fixed.

# col_names <- c("condition_occurrence_id",       
# "person_id",                     
# "condition_concept_id",         
# "condition_start_date",          
# "condition_start_datetime",      
# "condition_end_date",           
# "condition_end_datetime",        
# "condition_type_concept_id",     
# "condition_status_concept_id",  
# "stop_reason",                   
# "provider_id",                   
# "visit_occurrence_id",          
# "visit_detail_id",               
# "condition_source_value",        
# "condition_source_concept_id",  
# "condition_status_source_value")
# 
# co <- readr::read_tsv("synpuf1k_cdmv531/condition_occurrence.csv",
#                       col_names = col_names)
# 
# co2 <- co %>% 
#   mutate(condition_source_concept_id = condition_source_value) %>% 
#   mutate(condition_source_value = visit_detail_id) %>% 
#   mutate(visit_detail_id = NA_character_)
# 
# readr::write_tsv(co2, "synpuf1k_cdmv531/condition_occurrence_repaired.csv", na = "", col_names = F)

# As a first step write the ddl
# CommonDataModel::writeDdl("postgresql", "5.3", "ddl", cdmDatabaseSchema = "main")

con <- dbConnect(duckdb::duckdb(), dbdir = "synpuf1k.duckdb")
# con <- dbConnect(duckdb::duckdb()) # im-memory database


sql <- readr::read_file("ddl/OMOPCDM_postgresql_5.3_ddl.sql") %>% 
  tolower() %>% 
  stringr::str_split(";") %>% 
  {.[[1]]}

# sql <- sql[[1]][sql[[1]]>0]

# sql_split <- sql_split[sapply(sql_split, nchar) > 0]
r <- purrr::map(sql, ~purrr::safely(dbExecute)(con, .))

# check for errors
purrr::map(r, "error") %>% 
  purrr::keep(~!is.null(.))

dbListTables(con)

# does not seem to be case sensitive
dbGetQuery(con, "select * from concept")
dbGetQuery(con, "select * from CONCEPT")

# Load data into tables ------
csv_files <- list.files("synpuf1k_cdmv531") %>% 
  stringr::str_subset("csv") %>% 
  stringr::str_remove(".csv")

dbExecute(con, "COPY care_site FROM 'synpuf1k_cdmv531/care_site.csv' ( DELIMITER '\t');"                      )
dbExecute(con, "COPY cdm_source FROM 'synpuf1k_cdmv531/cdm_source.csv' ( DELIMITER '\t');"                    )
dbExecute(con, "COPY condition_era FROM 'synpuf1k_cdmv531/condition_era.csv' ( DELIMITER '\t');"              )
dbExecute(con, "COPY condition_occurrence FROM 'synpuf1k_cdmv531/condition_occurrence_repaired.csv' ( DELIMITER '\t');")
dbExecute(con, "COPY cost FROM 'synpuf1k_cdmv531/cost.csv' ( DELIMITER '\t');"                                )
dbExecute(con, "COPY death FROM 'synpuf1k_cdmv531/death.csv' ( DELIMITER '\t');"                              )
dbExecute(con, "COPY device_exposure FROM 'synpuf1k_cdmv531/device_exposure.csv' ( DELIMITER '\t');"          )
dbExecute(con, "COPY drug_era FROM 'synpuf1k_cdmv531/drug_era.csv' ( DELIMITER '\t');"                        )
dbExecute(con, "COPY drug_exposure FROM 'synpuf1k_cdmv531/drug_exposure.csv' ( DELIMITER '\t');"              )
dbExecute(con, "COPY location FROM 'synpuf1k_cdmv531/location.csv' ( DELIMITER '\t');"                        )
dbExecute(con, "COPY measurement FROM 'synpuf1k_cdmv531/measurement.csv' ( DELIMITER '\t');"                  )
dbExecute(con, "COPY observation_period FROM 'synpuf1k_cdmv531/observation_period.csv' ( DELIMITER '\t');"    )
dbExecute(con, "COPY observation FROM 'synpuf1k_cdmv531/observation.csv' ( DELIMITER '\t');"                  )
dbExecute(con, "COPY payer_plan_period FROM 'synpuf1k_cdmv531/payer_plan_period.csv' ( DELIMITER '\t');"      )
dbExecute(con, "COPY person FROM 'synpuf1k_cdmv531/person.csv' ( DELIMITER '\t');"                            )
dbExecute(con, "COPY procedure_occurrence FROM 'synpuf1k_cdmv531/procedure_occurrence.csv' ( DELIMITER '\t');")
dbExecute(con, "COPY provider FROM 'synpuf1k_cdmv531/provider.csv' ( DELIMITER '\t');"                        )
dbExecute(con, "COPY visit_occurrence FROM 'synpuf1k_cdmv531/visit_occurrence.csv' ( DELIMITER '\t');"  )

# seemed best to step through each load one by one to diagnose errors.
# sql <- purrr::map_chr(csv_files, ~glue::glue("COPY {.} FROM 'synpuf1k_cdmv531/{.}.csv' ( DELIMITER '\t');"))
# purrr::walk(sql, ~dbExecute(con, .))

dbDisconnect(con, shutdown = T)


con <- dbConnect(duckdb::duckdb(), "synpuf1k.duckdb")

dbListTables(con)
dbGetQuery(con, "select count(*) as n from person")
dbDisconnect(con, shutdown = T)
