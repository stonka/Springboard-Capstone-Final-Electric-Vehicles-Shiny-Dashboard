library(jsonlite)
library(dplyr)
library(readr)

ecs_api_orig_df <- stream_in(url("https://api.openchargemap.io/v2/poi/?output=json&maxresults=80000&compact=true&verbose=false"))

ecs_api_df <- cbind(
  ecs_api_orig_df$OperatorID,
  ecs_api_orig_df$UsageTypeID,
  ecs_api_orig_df$AddressInfo[,1],
  ecs_api_orig_df$AddressInfo[,2],
  ecs_api_orig_df$AddressInfo[,3],
  ecs_api_orig_df$AddressInfo[,4],
  ecs_api_orig_df$AddressInfo[,5],
  ecs_api_orig_df$AddressInfo[,6],
  ecs_api_orig_df$AddressInfo[,7],
  ecs_api_orig_df$AddressInfo[,8],
  ecs_api_orig_df$AddressInfo[,9],
  ecs_api_orig_df$NumberOfPoints,
  ecs_api_orig_df$StatusTypeID,
  ecs_api_orig_df$DateLastStatusUpdate,
  ecs_api_orig_df$DataQualityLevel,
  ecs_api_orig_df$DateCreated,
  ecs_api_orig_df$SubmissionStatusTypeID,
  ecs_api_orig_df$IsRecentlyVerified,
  ecs_api_orig_df$DateLastVerified,
  ecs_api_orig_df$DataProvidersReference,
  ecs_api_orig_df$DateLastConfirmed
)


colnames(ecs_api_df) <- c("operatorID",
                          "UsageTypeID",
                          "AddressInfoID",
                          "AddressInfoTitle",
                          "AddressInfoAddressLine1",
                          "AddressInfoTown",
                          "AddressInfoStateOrProvince",
                          "AddressInfoPostcode",
                          "AddressInfoCountryID",
                          "AddressInfoLatitude",
                          "AddressInfoLongitude",
                          "NumberOfPoints",
                          "StatusTypeID",
                          "DateLastStatusUpdate",
                          "DataQualityLevel",
                          "DateCreated",
                          "SubmissionStatusTypeID",
                          "IsRecentlyVerified",
                          "DateLastVerified",
                          "DataProvidersReference",
                          "DateLastConfirmed"
)

ecs_api_df <- as_data_frame(ecs_api_df)

write_csv(ecs_api_df, "ecs_api_df.csv" )
