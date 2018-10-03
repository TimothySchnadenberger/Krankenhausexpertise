# Krankenhausexpertise
library(xml2)

# import xml file
inputXML <- read_xml("260100023-06-2016-xml.xml")

# get name of hospital
krankenhaus_name <- xml_text(xml_find_all(pg, "//Krankenhaus/Kontaktdaten/Name"))

# extract all procedures and corresponding count
procedures <- xml_find_all(inputXML, '//Prozedur')
ops_codes <- lapply(procedures, xml_find_all, ".//OPS_301")
ops_codes <- lapply(ops_codes, xml_text)
ops_codes <- unlist(ops_codes)
numberOps <- lapply(procedures, xml_find_all, "./Anzahl")
numberOps <- lapply(numberOps, xml_text)
numberOps[!sapply(numberOps, function(y) length(y == 0))] <- NA
numberOps <- as.numeric(unlist(numberOps))
numberOps


# define columns for data frame
colnames_df = c("name", ops_codes)
colnames_df # print it

# create empty data frame
result_dataframe = data.frame()
# add new row (1) and add name of hospital
result_dataframe[1,"name"] = krankenhaus_name
# iterate through columns with ops codes, create new column for each 
# and fill it with corresponding count of procedures
for (i in 1:length(ops_codes)) {
  result_dataframe[1,ops_codes[i]] = numberOps[i]
}
