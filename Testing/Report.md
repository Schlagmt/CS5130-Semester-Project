# Report and Analysis

## RESTest Tests

In the paper, ["RESTest: Automated Black-Box Testing of RESTful Web APIs"](https://personal.us.es/amarlop/wp-content/uploads/2021/06/RESTest-Automated-Black-Box-Testing-of-RESTful-Web-APIs.pdf), the RESTest application is run against three different API's. For each API only one specific endpoint is tested, with both random testing and constraint based testing. In this section we will be rerunning these tests to verify the results found in the paper above.

## Recreating Tests

### Stripe: Create Product

#### Random Testing

![1](Recreate/Stripe/RT-Overview.png)
![1](Recreate/Stripe/RT-Categories.png)

#### Constraint Based Testing

![1](Recreate/Stripe/CBT-Overview.png)
![1](Recreate/Stripe/CBT-Categories.png)

#### Examination

|          | RT Pass | RT Fail | RT Pass Rate | CBT Pass | CBT Fail | CBT Pass Rate |
| -------- | ------- | ------- | ------------ | -------- | -------- | ------------- |
| Reported | 2000    | 0       | 100%         | 1465     | 535      | 73%           |
| Observed | 1908    | 92      | 95%          | 144      | 1856     | 7%            |



### Yelp: Search Businesses

#### Random Testing

![1](Recreate/Yelp/SearchBusiness-RT-Overview.png)
![1](Recreate/Yelp/SearchBusiness-RT-Categories.png)

#### Constraint Based Testing

![1](Recreate/Yelp/SearchBusiness-CBT-Overview.png)
![1](Recreate/Yelp/SearchBusiness-CBT-Categories.png)

#### Examination

|          | RT Pass | RT Fail | RT Pass Rate | CBT Pass | CBT Fail | CBT Pass Rate |
| -------- | ------- | ------- | ------------ | -------- | -------- | ------------- |
| Reported | 1933    | 67      | 97%          | 1839     | 161      | 92%           |
| Observed | 1751    | 249     | 88%          | 1034     | 966      | 52%           |



### Youtube: Search

#### Random Testing

![1](Recreate/Youtube/RT-Overview.png)

#### Constraint Based Testing

![1](Recreate/Youtube/CBT-Categories.png)
![1](Recreate/Youtube/CBT-Overview.png)

#### Examination

|          | RT Pass | RT Fail | RT Pass Rate | CBT Pass | CBT Fail | CBT Pass Rate |
| -------- | ------- | ------- | ------------ | -------- | -------- | ------------- |
| Reported | 2000    | 0       | 100%         | 1487     | 513      | 74%           |
| Observed | 2000    | 0       | 100%         | 540      | 1460     | 27%           |

## New Tests

### OMDb: 

#### Random Testing

![1](New/OMDb/RT-Overview.png)
![1](New/OMDb/RT-Categories.png)

#### Constraint Based Testing

![1](New/OMDb/CBT-Overview.png)
![1](New/OMDb/CBT-Categories.png)

#### Examination

|          | RT Pass | RT Fail | RT Pass Rate | CBT Pass | CBT Fail | CBT Pass Rate |
| -------- | ------- | ------- | ------------ | -------- | -------- | ------------- |
| Observed | 96      | 104     | 48%          | 92       | 108      | 46%           |

### Foursquare: 

#### Random Testing

![1](New/Foursquare/RT-Overview.png)
![1](New/Foursquare/RT-Categories.png)

#### Constraint Based Testing

![1](New/Foursquare/CBT-Overview.png)
![1](New/Foursquare/CBT-Categories.png)

#### Examination

|          | RT Pass | RT Fail | RT Pass Rate | CBT Pass | CBT Fail | CBT Pass Rate |
| -------- | ------- | ------- | ------------ | -------- | -------- | ------------- |
| Observed | 91      | 109     | 46%          | 70       | 130      | 35%           |