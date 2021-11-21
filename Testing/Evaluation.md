# Test Case Evaluation

## Results

|                   | RT Pass | RT Fail | RT Pass Rate | CBT Pass | CBT Fail | CBT Pass Rate |
| ----------------- | ------- | ------- | ------------ | -------- | -------- | ------------- |
| Stripe Reported   | 2000    | 0       | 100%         | 1465     | 535      | 73%           |
| Stripe Observed   | 1908    | 92      | 95%          | 144      | 1856     | 7%            |
| Yelp Reported     | 1933    | 67      | 97%          | 1839     | 161      | 92%           |
| Yelp Observed     | 1751    | 249     | 88%          | 1034     | 966      | 52%           |
| YouTube Reported  | 2000    | 0       | 100%         | 1487     | 513      | 74%           |
| YouTube Observed  | 2000    | 0       | 100%         | 540      | 1460     | 27%           |

## Stripe

In the recreation of the tests performed in ["RESTest: Automated Black-Box Testing of RESTful Web APIs"](https://personal.us.es/amarlop/wp-content/uploads/2021/06/RESTest-Automated-Black-Box-Testing-of-RESTful-Web-APIs.pdf) in relation to the Stripe API endpoint "Create Product", differences were found. Within random testing there was a difference in 5% in the test pass rate, the majority of this 5% is due to "OAS disconformity" errors. This is a custom error defined by RESTest. RESTest attempts to probe a 4XX error result from API's, it does this be sending information that does not align with the OAS form. In this case two extra fields were sent with random data, these being "tax_code" and "type". The API in question simply ignored the extra data and returned a "200 OK" response.

It can be argued that these "errors" reported by RESTest are invalid and not useful to developers. A core principle of REST API's is the idea of client-server autonomy, the client and server work independently. The separation means that API providers and API consumers can be modified, and it won’t backfire on their communication ([altexsoft](https://www.altexsoft.com/blog/rest-api-design/)). This designates that a server should not through an error if unnecessary data is provided, as long as the base requirements are met a valid response should be provided. A prime example is the rolling back of a required parameter. If an API is updated to no longer need a parameter, throwing an error upon receiving that parameter would coupe the client and server. Updates would need to be made to client systems, which goes against autonomy. A change to one system should not cause changes within the other. 

This suggests a failure in the RESTest design, unfortunately raw data is not reported for the raw results. This prevents us from determining why a local test resulted in "OAS Disconformities" while the reported results did not experience this. Furthermore it suggests that something was potentially changed within RESTest, that was not reported in there release notes ([RESTest Release Notes](https://github.com/isa-group/RESTest/releases/tag/restest-1.2.0)).When "OAS Disconformities" are removed from local results, random testing aligns within >1%.

When "OAS Disconformities" are removed from local results, constraint-based testing aligns within ~15%. This closes the large gap, but still leaves a semi-large gap between what is reported and what was observed. The next most common warning produced by RESTest is "Status 400 with (possibly) valid request", with observed results tallying 881 marked requests. This grouping is a fluid grouping because all fields being passed are correct, but inter-parameter dependencies may not be observed. While data is constrained it is still random to a degree which can explain this grouping changing size every time it is run. An update to the Stripe API was made August 27th, 2020, which could also can explain the changing results ([Stripe API Documentation](https://stripe.com/docs/upgrades)). In this update several new dependencies and error codes were added.

## Yelp

In reviewing the results from the observed testing a difference of 9% can be seen. The breakdown of errors is recorded below.

| Error                                                 | Count | 
| ----------------------------------------------------  | ----- | 
| Statues 5XX                                           | 183   | 
| Status 2XX with invalid request (invalid parameters)  | 37    |
| Status 5XX with invalid request                       | 24    | 
| Disconformity with OAS                                | 5     | 

As noted above the validity of "OAS Disconformities" can be called into question and cannot be verified against the results reported in the academic paper above. Based on the values provided above, it can be reasoned that the difference between the observed and reported results is the error code "Status 5XX". When taken away the results align by >1%. This suggests that these errors were not present in the reported results and upon deeper investigation the error causing this status is something that can be taken out, in constraint-based testing. This fails to explain how the reported results didn't see a high number of these errors. The error itself is cause by an optional field, "offset", which as defined by Yelp "Offsets the list of returned business results by this amount" ([Yelp Documentation](https://www.yelp.com/developers/documentation/v3/business_search)). Later down on the page Yelp states "Using the offset and limit parameters, you can get up to 1000 businesses from this endpoint if there are more than 1000 results. If you request a page out of this 1000 business limit, this endpoint will return an error". This is the technicality causing the difference in observed and reported results. There is no evidence of an internal Yelp API update that could cause this difference ([Yelp Changelog](https://www.yelp.com/developers/v3/changelog)). 

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

## Youtube

Of the three tests performed in this study, YouTube was by far the hardest to reproduce and to determine the validity of. Incorrect formatting of data created calls that could not be properly received by the YouTube API. Furthermore, YouTube's API imposes a limit of the number of calls that can be made in a single day by a user. This limit is under the number of tests required to meet the results showed in the paper. This calls into question how the authors were able to run this many tests in one sitting. Also, for random testing and constraint-based testing if the TestConf files provided are unaltered then the results do not match expected results at all. There are a number of updates that have been made within the last year that can help to explain the differences ([YouTube API Changelog](https://developers.google.com/youtube/v3/revision_history)). 