# Project Report

## Project Description

For this semester project I focused on the study and recreation of the tests performed in the academic paper ["RESTest: Automated Black-Box Testing of RESTful Web APIs"](https://personal.us.es/amarlop/wp-content/uploads/2021/06/RESTest-Automated-Black-Box-Testing-of-RESTful-Web-APIs.pdf). RESTest is a Java based application that is designed to provide a more precise form of testing RESTful APIs. It takes in a [OpenAPI Specifications (OSA)](https://swagger.io/specification/) file and is able to create a test configuration file for the endpoints listed in the file. This test configuration file can then be edited by the developer to create more in depth and specific test cases for the API. RESTest can be run in a variety of different forms, including the primary options of random based testing and constraint-based testing, which makes use of the custom values defined in the test configuration file. These tests can be displayed and examined through custom Allure web pages that are built at run time.  

Personally, I chose this project because of the growing demand for APIs in web development. In my last co-op rotation, I spent the majority of my time learning, designing, and implementing a RESTful API. A major part of this process is designing and building tests to assure the API is functioning properly. The prospect of an automated tool has the potential to save a large amount of time and effort. However, this process must be thoroughly checked and built upon to assure its functionality and reliability in practice.  This project will focus on the recreation of tests published in the preceding academic paper to assure the quality of the RESTest tool. Any differences between the reported and observed results will be subject to discussion and further investigation. 

## Test Case Reproduction & Evaluation

### Reproduction

In the paper above, the RESTest application is tested against three different API's. For each API only one specific endpoint is tested, with both random testing and constraint-based testing. Each test consists of 2000 test cases. This report will focus on two of the three API's that were tested, this is because of limitations imposed by the third API, Youtube. YouTube's API imposes a limit of the number of calls that can be made in a single day by a user. This limit is under the number of tests required to meet the results showed in the paper.

|                   | RT Pass | RT Fail | RT Pass Rate | CBT Pass | CBT Fail | CBT Pass Rate |
| ----------------- | ------- | ------- | ------------ | -------- | -------- | ------------- |
| Stripe Reported   | 2000    | 0       | 100%         | 1465     | 535      | 73%           |
| Stripe Observed   | 1908    | 92      | 95%          | 144      | 1856     | 7%            |
| Yelp Reported     | 1933    | 67      | 97%          | 1839     | 161      | 92%           |
| Yelp Observed     | 1751    | 249     | 88%          | 1034     | 966      | 52%           |

Above is the results of tests run within the paper (Reported) and tests run locally (Observed). For further detail in regards to results please refer to the [Report.md](https://github.com/Schlagmt/CS5130-Semester-Project/blob/main/Testing/Report.md) file in the testing directory.


### Evaluation

## Possible Improvements

## Conclusion 

