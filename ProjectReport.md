# Project Report

## RESTest Overview 

- [Code Review](CodeEvaluation/CodeReview.md)

## Test Case Reproduction 

- [Testing](Testing/Report.md)

## Test Case Evaluation

- [Testing](Testing/Report.md)

### Results

|                   | RT Pass | RT Fail | RT Pass Rate | CBT Pass | CBT Fail | CBT Pass Rate |
| ----------------- | ------- | ------- | ------------ | -------- | -------- | ------------- |
| Stripe Reported   | 2000    | 0       | 100%         | 1465     | 535      | 73%           |
| Stripe Observed   | 1908    | 92      | 95%          | 144      | 1856     | 7%            |
| Yelp Reported     | 1933    | 67      | 97%          | 1839     | 161      | 92%           |
| Yelp Observed     | 1751    | 249     | 88%          | 1034     | 966      | 52%           |
| YouTube Reported  | 2000    | 0       | 100%         | 1487     | 513      | 74%           |
| YouTube Observed  | 2000    | 0       | 100%         | 540      | 1460     | 27%           |

### Stripe

In the recreation of the tests performed in ["RESTest: Automated Black-Box Testing of RESTful Web APIs"](https://personal.us.es/amarlop/wp-content/uploads/2021/06/RESTest-Automated-Black-Box-Testing-of-RESTful-Web-APIs.pdf) in relation to the Stripe API endpoint "Create Product", differences were found. Within random testing there was a difference in 5% in the test pass rate, the majority of this 5% is due to "OAS disconformity" errors. This is a custom error defined by RESTest. RESTest attempts to probe a 4XX error result from API's, it does this be sending information that does not align with the OAS form. In this case two extra fields were sent with random data, these being "tax_code" and "type". The API in question simply ignored the extra data and returned a "200 OK" response.

It can be argued that these "errors" reported by RESTest are invalid and not useful to developers. A core principle of REST API's is the idea of client-server autonomy, the client and server work independently. The separation means that API providers and API consumers can be modified, and it won’t backfire on their communication ([altexsoft](https://www.altexsoft.com/blog/rest-api-design/)). This designates that a server should not through an error if unnecessary data is provided, as long as the base requirements are met a valid response should be provided. A prime example is the rolling back of a required parameter. If an API is updated to no longer need a parameter, throwing an error upon receiving that parameter would coupe the client and server. Updates would need to be made to client systems, which goes against autonomy. A change to one system should not cause changes within the other. 

This suggests a failure in the RESTest design, unfortunately raw data is not reported for the raw results. This prevents us from determining why a local test resulted in "OAS Disconformities" while the reported results did not experience this. Furthermore it suggests that something was potentially changed within RESTest, that was not reported in there release notes ([RESTest Release Notes](https://github.com/isa-group/RESTest/releases/tag/restest-1.2.0)).When "OAS Disconformities" are removed from local results, random testing aligns within >1%.

When "OAS Disconformities" are removed from local results, constraint-based testing aligns within ~15%. This closes the large gap, but still leaves a semi-large gap between what is reported and what was observed. The next most common warning produced by RESTest is "Status 400 with (possibly) valid request", with observed results tallying 881 marked requests. This grouping is a fluid grouping because all fields being passed are correct, but inter-parameter dependencies may not be observed. While data is constrained it is still random to a degree which can explain this grouping changing size every time it is run. An update to the Stripe API was made August 27th, 2020, which could also can explain the changing results ([Stripe API Documentation](https://stripe.com/docs/upgrades)). In this update several new dependencies and error codes were added.

### Yelp

### Youtube

## Possible Improvements

## Conclusion 

