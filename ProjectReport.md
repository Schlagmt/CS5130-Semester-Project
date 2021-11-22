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

## Stripe

Within random testing a difference of 5% is seen in the test pass rate between the reported and observed results, the majority of this 5% is due to "OAS disconformity" errors. This is a custom error defined by RESTest. RESTest attempts to probe a 4XX error result from API's, it does this be sending information that does not align with the OAS form. In this case two extra fields were sent with random data, these being "tax_code" and "type". The API in question simply ignored the extra data and returned a "200 OK" response.

| Error                                                 | Count | 
| ----------------------------------------------------  | ----- | 
| Disconformity with OAS                                | 92    | 

It can be argued that these "errors" reported by RESTest are invalid and not useful to developers. A core principle of REST API's is the idea of client-server autonomy, the client and server work independently. This separation means that API providers and API consumers can be modified, and it won’t backfire on their communication ([altexsoft](https://www.altexsoft.com/blog/rest-api-design/)). This designates that a server should not throw an error if unnecessary data is provided, as long as the base requirements are met a valid response should be provided. A prime example is the rolling back of a required parameter. If an API is updated to no longer need a parameter, throwing an error upon receiving that parameter would coupe the client and server. Updates would need to be made to client systems, which goes against autonomy. A change to one system should not cause change requirements within the other. 

This suggests a failure in the RESTest design, the validity of these errors cannot be compared to the reported statistics because RESTest didn't release the raw results for random testing. That being said no constraint-based testing raw results contain the "OAS disconformity", suggesting that something was potentially changed within RESTest, that was not reported in there release notes ([RESTest Release Notes](https://github.com/isa-group/RESTest/releases/tag/restest-1.2.0)) or there are errors in reproduction. When "OAS Disconformities" are removed from local results, random testing aligns within <1%.

When "OAS Disconformities" are removed from local results, constraint-based testing aligns within ~15%. This closes the large gap, but still leaves a semi-large gap between what is reported and what was observed.

| Error                                                                   | Count | 
| ----------------------------------------------------------------------  | ----- | 
| Disconformity with OAS                                                  | 917   |
| Statues 4XX with (possibly) valid credentials                           | 881   | 
| Status 2XX with invalid request (inter-parameters dependency violated)  | 58    |
 

The next most common warning produced by RESTest is "Status 4XX with (possibly) valid request", with observed results tallying 881 marked requests. This grouping is a fluid grouping because all fields being passed are correct, but inter-parameter dependencies may not be observed. While data is constrained it is still random to a degree which can explain this grouping changing size every time it is run. This is confirmed by the raw results provided by [RESTest](https://github.com/isa-group/icsoc-2020-supplementary-material), which shows all 535 errors as "Status 4XX with (possibly) valid request". An update to the Stripe API was made August 27th, 2020, which could also can explain the changing results ([Stripe API Documentation](https://stripe.com/docs/upgrades)). In this update several new dependencies and error codes were added.

## Yelp

In reviewing the results from the observed random testing, a difference of 9% can be seen. The breakdown of errors is recorded below.

| Error                                                 | Count | 
| ----------------------------------------------------  | ----- | 
| Statues 5XX                                           | 183   | 
| Status 2XX with invalid request (invalid parameters)  | 37    |
| Status 5XX with invalid request                       | 24    | 
| Disconformity with OAS                                | 5     | 

As noted above the validity of "OAS Disconformities" can be called into question and cannot be verified against the results reported in the academic paper above. Based on the values provided above, it can be reasoned that the difference between the observed and reported results is the error code "Status 5XX". When taken away the results align by >1%. This suggests that these errors were not present in the reported results and upon deeper investigation the error causing this status is something that can be taken out, in constraint-based testing. This fails to explain how the reported results didn't see a high number of these errors. The error itself is caused by an optional field, "offset", which as defined by Yelp "Offsets the list of returned business results by this amount" ([Yelp Documentation](https://www.yelp.com/developers/documentation/v3/business_search)). Later down on the page Yelp states "Using the offset and limit parameters, you can get up to 1000 businesses from this endpoint if there are more than 1000 results. If you request a page out of this 1000 business limit, this endpoint will return an error". This is the technicality causing the difference in observed and reported results. There is no evidence of an internal Yelp API update that could cause this difference ([Yelp Changelog](https://www.yelp.com/developers/v3/changelog)). 

Constraint based testing again shows a large difference between reported and locally observed results, making for a 40% drop in pass rating. The breakdown of errors is recorded below.

| Error                                                                 | Count | 
| --------------------------------------------------------------------- | ----- | 
| Status 5XX with valid request                                         | 404   |
| Status 2XX with invalid request (inter-parameter dependency violated) | 211   | 
| Status 5XX with invalid request                                       | 192   | 
| Status 400 with (possibly) valid request                              | 121   | 
| Status 2XX with invalid request (invalid parameter)                   | 28    | 
| Disconformity with OAS                                                | 10    |

A difference of 40% cannot be made up or explained across anyone individual error listed above, which means the differences in results is a combination of inconsistencies. First based on prior analysis we can call into question the validity of "Status 5XX with valid request" and "Disconformity with OAS". 

The validity of "Status 2XX with invalid request (inter-parameter dependency violated)" can be called into question because of the inconsistency of API design and definitions ([StackExchange](https://softwareengineering.stackexchange.com/questions/329229/should-i-return-an-http-400-bad-request-status-if-a-parameter-is-syntactically)). Based on most resent documentation of HTTP error codes, a 4XX response "indicates that the server cannot or will not process the request due to something that is perceived to be a client error (e.g., malformed request syntax, invalid request message framing, or deceptive request routing)." ([RFC](https://www.rfc-editor.org/rfc/rfc7231)). This shows that failure to meet the standard business logic of an API is not a valid reason for a 4XX call. It is also worth noting the Yelp API returned no valid business for these requests.

The validity of "Status 5XX with invalid request" can be assured. Given our last error call we know that invalid request message framing should result in a 4XX error code. These requests are pairing incorrect data types with parameters, and accordingly receiving 5XX error codes instead of 4XX error codes.

## Possible Improvements

There are a number of things that RESTest can implement into its design to create a better more effective RESTful API testing service. In this section I will define three key areas for improvement.

1. Improved Documentation: In the process of researching, building, and reproducing RESTest test documentation lacked information on the meaning and importance of errors. Multiple error messages are vague and lack explanation for why they are considered errors. The prime example is "OAS Disconformities", these errors are casually mentioned in the paper, but are not explained and do not appear in the raw results provided by the developers. 
2. Updated Benchmarking: The results presented in the benchmark results do not appear to be run in the same version of RESTest that is currently available. For proper reproducibility updated benchmarking must be done or else new results cannot be easily compared to older versions.
3. Usefulness: The validity and value of an error message grouping is not standard and can be determined to be different on a case-by-case basis. Making certain test cases virtually useless. RESTest must implement a strategy of displaying the usefulness in each error message.

## Threats to Validity

When reproducing tests performed in an academic paper there are many areas where error and difference can occur. Throughout this process there were many areas where a lack of information made perfect reproduction difficult. First, being the differences in versions of the applications, the tests performed in this study were done on version 1.3.0 while the reported results were performed on an older version. The addition of new techniques and error detection tools can lead to different results. Secondly, the test configuration files used by the program could have potentially had manual changes that could not be reproduced in this report. [RESTest](https://github.com/isa-group/icsoc-2020-supplementary-material/tree/master/oas_specifications) provided the initial OAS documents for each API, but they didn't include test configuration and properties files. This can drastically change what the program does and the specific tests that are generated. Lastly, a threat is a lack of background knowledge by the individual performing the study. I am still new to the RESTful API development process and testing cycle, errors and setting maybe occurring that I didn't have the background knowledge to catch.

## Conclusion 

In conclusion, the reproducibility of the tests performed in the paper ["RESTest: Automated Black-Box Testing of RESTful Web APIs"](https://personal.us.es/amarlop/wp-content/uploads/2021/06/RESTest-Automated-Black-Box-Testing-of-RESTful-Web-APIs.pdf) cannot be verified. Despite this fact, the results obtained from this report show that the RESTest tool is viable and useful in showing potential bugs within the coding and handling of API requests. Software that can find disconformities is valuable in API development as it can help to produce a product that is easier to understand for the client. It also makes the API more predictable and less inconsistent in error status and messages.