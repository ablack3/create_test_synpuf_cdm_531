This repository contains a small OMOP CDM in a duckdb database. duckdb
is a file based database similar to SQLite but with the benefit of a
date type and a OLAP optimized design both of which are important for
OMOP CDM data.

How to use this resource:

1.  Download the file `synpuf1k.duckdb`
2.  Connect to and query the CDM in R

<!-- -->

    library(DBI)

    con <- dbConnect(duckdb::duckdb(), "synpuf1k.duckdb")

    dbListTables(con)

    ##  [1] "attribute_definition"  "care_site"             "cdm_source"           
    ##  [4] "cohort_definition"     "concept"               "concept_ancestor"     
    ##  [7] "concept_class"         "concept_relationship"  "concept_synonym"      
    ## [10] "condition_era"         "condition_occurrence"  "cost"                 
    ## [13] "death"                 "device_exposure"       "domain"               
    ## [16] "dose_era"              "drug_era"              "drug_exposure"        
    ## [19] "drug_strength"         "fact_relationship"     "location"             
    ## [22] "measurement"           "metadata"              "note"                 
    ## [25] "note_nlp"              "observation"           "observation_period"   
    ## [28] "payer_plan_period"     "person"                "procedure_occurrence" 
    ## [31] "provider"              "relationship"          "source_to_concept_map"
    ## [34] "specimen"              "visit_detail"          "visit_occurrence"     
    ## [37] "vocabulary"

    dbGetQuery(con, "select * from condition_occurrence limit 10")

    ##    condition_occurrence_id person_id condition_concept_id condition_start_date
    ## 1                        4         1                80502           2010-03-12
    ## 2                       12         1               134736           2009-07-25
    ## 3                       15         1               194133           2009-07-25
    ## 4                       16         1                30234           2010-08-17
    ## 5                       10         1               433990           2010-11-05
    ## 6                        6         1               435510           2009-10-14
    ## 7                        2         1               319835           2010-03-12
    ## 8                       11         1               435783           2010-11-05
    ## 9                        5         1               437469           2010-03-12
    ## 10                       9         1               436665           2010-04-01
    ##    condition_start_datetime condition_end_date condition_end_datetime
    ## 1                2010-03-12         2010-03-13                   <NA>
    ## 2                2009-07-25         2009-07-25                   <NA>
    ## 3                2009-07-25         2009-07-25                   <NA>
    ## 4                2010-08-17         2010-08-17                   <NA>
    ## 5                2010-11-05         2010-11-05                   <NA>
    ## 6                2009-10-14         2009-10-14                   <NA>
    ## 7                2010-03-12         2010-03-13                   <NA>
    ## 8                2010-11-05         2010-11-05                   <NA>
    ## 9                2010-03-12         2010-03-13                   <NA>
    ## 10               2010-04-01         2010-04-01                   <NA>
    ##    condition_type_concept_id condition_status_concept_id stop_reason
    ## 1                   38000200                          NA           1
    ## 2                   38000230                          NA           6
    ## 3                   38000230                          NA           6
    ## 4                   38000230                          NA           7
    ## 5                   38000230                          NA           5
    ## 6                   38000230                          NA           3
    ## 7                   38000200                          NA           1
    ## 8                   38000230                          NA           5
    ## 9                   38000200                          NA           1
    ## 10                  38000230                          NA           4
    ##    provider_id visit_occurrence_id visit_detail_id condition_source_value
    ## 1            1                  NA              NA                  73300
    ## 2            6                  NA              NA                   7245
    ## 3            6                  NA              NA                   7242
    ## 4            7                  NA              NA                   8470
    ## 5            5                  NA              NA                  29521
    ## 6            3                  NA              NA                  27541
    ## 7            1                  NA              NA                   4280
    ## 8            5                  NA              NA                  29590
    ## 9            1                  NA              NA                  E9330
    ## 10           4                  NA              NA                  29680
    ##    condition_source_concept_id condition_status_source_value
    ## 1                     44822235                             0
    ## 2                     44825708                             0
    ## 3                     44819973                             0
    ## 4                     44825905                             0
    ## 5                     44834586                             0
    ## 6                     44828807                             0
    ## 7                     44826642                             0
    ## 8                     44829921                             0
    ## 9                     44831870                             0
    ## 10                    44822981                             0

    dbDisconnect(con, shutdown = TRUE)

duckdb can be used in python, Java, C++, or from the command line. Check
out the documentation at <https://duckdb.org/docs/api/overview>
