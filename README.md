# CS5130-Semester-Project
Replication of RESTest with Evaluation

## Source
- ["RESTest: Automated Black-Box Testing of RESTful Web APIs"](https://personal.us.es/amarlop/wp-content/uploads/2021/06/RESTest-Automated-Black-Box-Testing-of-RESTful-Web-APIs.pdf)
- [RESTest GitHub](https://github.com/isa-group/RESTest)

## Reproduction of Tests
In order to reproduce the results found in this repository follow the directions listed below...
1. Proceed to [RESTest's release page on GitHub](https://github.com/isa-group/RESTest/releases/tag/restest-1.2.0) and download restest-1.2.0.zip (Note: Requires Java 8), once downloaded unzip the folder
2. The configuration files needed to run the tests in this repository can be in the following folders...<br>
[Foursquare](https://github.com/Schlagmt/CS5130-Semester-Project/tree/main/Testing/New/Foursquare/Config), [OMDb](https://github.com/Schlagmt/CS5130-Semester-Project/tree/main/Testing/New/OMDb/Config), [Stripe](https://github.com/Schlagmt/CS5130-Semester-Project/tree/main/Testing/Recreate/Stripe/Config), [Yelp](https://github.com/Schlagmt/CS5130-Semester-Project/tree/main/Testing/Recreate/Yelp/Config), [Youtube](https://github.com/Schlagmt/CS5130-Semester-Project/tree/main/Testing/Recreate/Youtube/Config) <br>
Download the files for the test and place them in a folder with the name corresponding to the folder from GitHub in the file path "restest-1.2.0\src\test\resources"
3. All the tests performed require authorization from the source API. In the "restest-1.2.0\src\test\resources" directory there is an auth folder, copy the contents of the [auth folder on GitHub](https://github.com/Schlagmt/CS5130-Semester-Project/tree/main/Testing/auth) to this auth folder
4. Below are links to all the relevant pages to learn how to get authorization, put the token into the corresponding apikeys.json or headers.json files<br>
[Foursquare](https://developer.foursquare.com/docs/places-api-getting-started), [OMDb](http://www.omdbapi.com/apikey.aspx?__EVENTTARGET=freeAcct), [Stripe](https://stripe.com/docs/api/authentication), [Yelp](https://www.yelp.com/developers/documentation/v3/authentication), [Youtube](https://developers.google.com/youtube/registering_an_application) <br>
5. Once all conditions are met run the following code in command line, results are outputted to Target folder
```
java -jar restest.jar src/test/resources/%FOLDER_NAME%/%.PROPERTIES FILE%
```

## Deliverables
### Deliverable 1 (09/30)
- [Code Review](CodeEvaluation/CodeReview.md)

### Deliverable 2 (10/28)
- [Testing](Testing/Report.md)

### Deliverable 3 (11/18)
- [Evaluation](Testing/Evaluation.md)

### Project Report (11/30)
- [Project Report](ProjectReport.md)
- [Presentation]()