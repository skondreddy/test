---
layout: default
title: FTR Submissions API
nav_order: 1
parent: FTR Submissions
---

# FTR Submissions API
[Access API JSON](/docs/API/FTRSubmissions/index.json)


## Changelog

### 0.9
* move dev/test endpoint to Merry (see spec)

### 0.8
* add to Submission schema:
    * *caID* (**required**)
    * *marketName*
    * *auctionName*
    * *auctionRound*
* unflag as **required**:
    * *ftrMarketID*
    * *ftrAuctionID*

### 0.7
* flag tou as **required**
* update name of property from *strategy* to *strategyID*
* update name of property from *ftrSubmissionId* to *ftrSubmissionID*
* update name of property from *userId* to *userID*

### 0.6
* added 404 response to delete request if no ids found

### 0.5
* separate userId from Submission object - add to top level of DELETE and POST request objects

### 0.4
* corrected some Submission property types
* added associated db columns to some property descriptions

### 0.3
* Misc Corrections
* defined dev/test endpoint

### 0.2
* Misc Corrections

### 0.1
* Initial draft