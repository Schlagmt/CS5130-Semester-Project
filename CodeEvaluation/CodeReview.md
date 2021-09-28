# RESTest Code Review

## Process

### OpenAPI Specifications

To run RESTest, the user must provide the [OpenAPI Specifications (OSA)](https://swagger.io/specification/) for the designated RESTful API. OSA, in short, is used to define a common language for RESTful API that can be read and used by both programmer and computer. The example below shows part of a Marvel OSA, formatted as a JSON object.

```JSON
host: gateway.marvel.com
basePath: "/"
schemes:
- https
swagger: '2.0'
info:
  title: gateway.marvel.com
  version: Cable
tags:
- name: public
paths:
  "/v1/public/characters/{characterId}":
    get:
      tags:
      - public
      operationId: getCharacterIndividual
      summary: Fetches a single character by id.
      description: This method fetches a single character resource.  It is the canonical
        URI for any character resource provided by the API.
      parameters:
      - in: path
        description: A single character id.
        name: characterId
        required: true
        type: integer
        format: int32
      responses:
        '200':
          description: No response was specified
          schema:
            "$ref": "#/definitions/CharacterDataWrapper"
        '404':
          description: Character not found.
```

This file is able to be parsed by an additional library that is included in RESTest called [Swagger Parser](https://github.com/swagger-api/swagger-parser#overview). 

### Test Configuration File

This OpenAPISpecification object is then passed into a [configuration class](https://github.com/isa-group/RESTest/blob/master/src/main/java/es/us/isa/restest/main/CreateTestConf.java). This class will generate a TestConf.yaml file which will be used to create all of the test data and test cases later on. The file generated can be broken down by method defined by the API in the OSA file. 

```JSON
- testPath: /endpoint/path
  operations:
  - operationId: getSomething
    method: get
    testParameters:
    - name: location 
      weight: 0.5 
      generator: 
        type: RandomEnglishWord
        genParameters:
        - name: maxWords
          values:
          - 1
```

Within each path being tested the following information is defined...
- [Method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) - The HTTP method defines what type of operation is being done by the end point. In most cases it is one of the following CRUD (Create, Read, Update, Delete) based operations; POST, GET, PUT, or DELETE.

Test Parameters - This refer to the data that is passed in following the end point URL.
- Name - Refers to the name of the variable being passed in.
- [Weight](https://github.com/isa-group/RESTest/wiki/Test-configuration-files#parameter-weights) - A parameter unique to RESTest used to place a weight on certain parameters when testing. The higher the weight the more focus will be placed on that specific variable and endpoint.

Generator - This refers to the type of how the given variables data will be generated. 
- [Type](https://github.com/isa-group/RESTest/wiki/Test-configuration-files#test-data-generators) - this determines what kind of data is going to be generated for the given variable. The first part deals with the variables type, this can be things like strings, integers, doubles, and Booleans. Secondly, it determines how it is being tested, randomly vs. boundary. Random simply creates random data, while boundary can be used to test the limits of the size of the input. 

GenParameters - This section is used to define rules about the parameters.
- Name - A keyword used to determine what rules are being enforced on the variable. In the above case the keyword in "maxWords", this limits how many words can be passed in. These primarily are used for limiting numerical inputs and textual inputs.
- Values - The corresponding value to the given rule defined.

Once each endpoint has been reviewed and added to the TestConf.yaml, the document will be created. At this point the user is also able to edit the file and change parameters as they please. 

### Properties Configuration

RESTest allows for multiple properties and settings to be change by the user prior to API testing. This can include basic items like the number of tests to be generated and whether or not in-depth statistics are desired. An example and layout can be found [here](https://github.com/isa-group/RESTest/wiki/RESTest-configuration-files). These are saved in a .properties document in the same folder the TestConf.yml is generated within.

### Test Generation and Execution

This is where the bulk of the RESTest work is done, it's first job is primarily set-up work. This includes, reading in the previously defined .properties and the TestConf.ymal files along with creating an output directory for all results.

The RESTest initializes the runner class which will be used to create the test cases. The runner class proceeds to generate abstract test cases for each path and method in the api as defined in the OSA file.

#### Generation

In the generation section the code begins to work through the TestConf.yaml file endpoint by endpoint. With each variable type a different function is called to create a unique value.

Random
- [RandomBooleanGenerator](https://github.com/isa-group/RESTest/blob/master/src/main/java/es/us/isa/restest/inputs/random/RandomBooleanGenerator.java) - Uses random number generation to generate a one or zero value. If one, the preset value is overridden and set to 'false'.
- [RandomDateGenerator](https://github.com/isa-group/RESTest/blob/master/src/main/java/es/us/isa/restest/inputs/random/RandomDateGenerator.java) - Uses the [SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html) from java.text to determine a date based a programmatically generated long value. This class also can use a startDate and endDate if defined by the user in the TestConf.yaml for the given variable. 
- [RandomEnglishWordGenerator](https://github.com/isa-group/RESTest/blob/master/src/main/java/es/us/isa/restest/inputs/random/RandomEnglishWordGenerator.java) - Uses the [Dictionary class](https://docs.oracle.com/javase/7/docs/api/java/util/Dictionary.html) provided by java.util. Based on the minWords and maxWords defined in TestConf.yaml a string of words is generated. A random part of speech is selected, and then a dictionary is queued for a random word in that part of speech.Depending on the length linking words (the, a , so, for, etc.) can be used to join words.  
- [RandomGenerator](https://github.com/isa-group/RESTest/blob/master/src/main/java/es/us/isa/restest/inputs/random/RandomGenerator.java) - Provides and refresh the random seed used for generating random numbers. This is done through the java.lang library with the class [RandomDataGenerator](https://commons.apache.org/proper/commons-math/javadocs/api-3.6/org/apache/commons/math3/random/RandomDataGenerator.html)
- [RandomNumberGenerator](https://github.com/isa-group/RESTest/blob/master/src/main/java/es/us/isa/restest/inputs/random/RandomNumberGenerator.java) - Uses random number generation to generate all types of numbers (integer, double, float, and long).
- [RandomObjectGenerator](https://github.com/isa-group/RESTest/blob/master/src/main/java/es/us/isa/restest/inputs/random/RandomObjectGenerator.java) - Uses random number generation to select an object out of a given list of objects
- [RandomRegExpGenerator](https://github.com/isa-group/RESTest/blob/master/src/main/java/es/us/isa/restest/inputs/random/RandomRegExpGenerator.java) - Takes in a given regular expression and generates a valid input. This primarily makes use of the [Generex](https://github.com/mifmif/Generex) library which can create strings from regular expressions. 
- [RandomStringGenerator](https://github.com/isa-group/RESTest/blob/master/src/main/java/es/us/isa/restest/inputs/random/RandomStringGenerator.java) - Different from RandomEnglishWordGenerator, this uses [RandomStringUtil](http://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/RandomStringUtils.html) from java.lang, and creates more complex strings. Based on data from TestConf.yaml, this can create specific length strings whose values are ASCII value is between 32 and 126 (inclusive), Latin alphabetic characters, Latin alphabetic characters (a-z, A-Z) with the digits 0-9, or numeric characters. It can also provide the empty string.

\* All random number generation is done through java.util's [Random class](https://docs.oracle.com/javase/8/docs/api/java/util/Random.html)

Once the data generators have been initialized and test data has been collected, it is time for the assembly of the HTTP requests that will ping the RESTful API to be constructed. RESTest creates an array of [TestCases](https://github.com/isa-group/RESTest/blob/master/src/main/java/es/us/isa/restest/testcases/TestCase.java), a class with values for all the applicable HTTP parameters. It is provided a unique ID upon creation and the code adds the test data along with the proper endpoint. Also properly sorting URL parameter data and potential HTTP body data to be sent in the call. 

TestCase also contains validation checks to assure that each test is properly built and will not fail due to incorrect values or syntax. Once all tests are completely generated, they are returned to the runner. From there the runner writes all the tests to a .java file for later execution.

#### Execution

```Java
JUnitCore junit = new JUnitCore();
junit.addListener(new io.qameta.allure.junit4.AllureJunit4());
Timer.startCounting(TEST_SUITE_EXECUTION);
Result result = junit.run(testClass);
Timer.stopCounting(TEST_SUITE_EXECUTION);
```

RESTest makes use of the [JUnitCore](http://junit.sourceforge.net/javadoc/org/junit/runner/JUnitCore.html) class with the added listener [AllureJunit4](https://github.com/allure-framework/allure-java/blob/master/allure-junit4/src/main/java/io/qameta/allure/junit4/AllureJunit4.java). Each test is run and the results are recorded for report generation.

#### Reports

