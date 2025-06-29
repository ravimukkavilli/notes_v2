#  Test Pyramid



[The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html)

-  A well-rounded test portfolio should look like to be responsive, reliable and maintainable - regardless of whether you're building a microservices architecture, mobile apps or IoT ecosystems.
-  **Continuous delivery**, a practice where you automatically ensure that your software can be released into production any time, can help you with that. With continuous delivery you use a `build pipeline` to automatically test your software and deploy it to your testing and production environments.
-  Traditionally software testing was overly manual work done by deploying your application to a test environment and then performing some black-box style testing e.g. by clicking through your user interface to see if anything's broken.
-  It's obvious that testing all changes manually is time-consuming, repetitive and tedious.
-  Automate your tests and you can change your codebase without batting an eye.

  ![IMAGE](https://github.com/ravimukkavilli/notes/blob/main/testPyramid.png)

-  Stick to the pyramid shape to come up with a healthy, fast and maintainable test suite: Write lots of small and fast unit tests. Write some more coarse-grained tests and very few high-level tests that test your application from end to end.

### Unit tests

-  The foundation of your test suite will be made up of unit tests. Your unit tests make sure that a certain unit (your subject under test) of your codebase works as intended. Unit tests have the narrowest scope of all the tests in your test suite.
-  Two sorts of tests as `solitary unit tests` for tests that stub all collaborators and `sociable unit tests` for tests that allow talking to real collaborators.
-  Stick to the **one test class per production class** rule.
-  A unit test class should at least test the **public interface of the class**.
-  Unit tests should ensure that all your `non-trivial` code paths are tested (including happy path and edge cases). At the same time they shouldn't be tied to your implementation too closely.
-  Don't reflect your internal code structure within your unit tests. Test for observable behaviour instead. Think about
    `if I enter values x and y, will the result be z?`
  instead of
    `if I enter x and y, will the method call class A first, then call class B and then return the result of class A plus the result of class B?`
 -  Private methods should generally be considered an implementation detail.

### Mocking and Stubbing
*  Mocks and Stubs are two different kinds of Test Doubles.
*  Different kind of test doubles are:
    *  **Dummy** objects are passed around but never actually used. Usually they are just used to fill parameter lists.
    *  **Fake** objects actually have working implementations, but usually take some shortcut which makes them not suitable for production (an                 InMemoryTestDatabase is a good example).
    *  **Stubs** provide canned answers to calls made during the test, usually not responding at all to anything outside what's programmed in for the test.
    *  **Spies** are stubs that also record some information based on how they were called. One form of this might be an email service that records how many messages it was sent.
    *  **Mocks** are pre-programmed with expectations which form a specification of the calls they are expected to receive. They can throw an exception if they receive a call they don't expect and are checked during verification to ensure they got all the calls they were expecting.
 
### Test Structure

*  A good structure for all your tests (this is not limited to unit tests) is this one:
    1.  Set up the test data
    2.  Call your method under test
    3.  Assert that the expected results are returned
*   `“given”`, `“when”`, `“then”` triad, where given reflects the setup, when the method call and then the assertion part.
*   

### Integration Tests

*  test one integration point at a time by replacing separate services and databases with test doubles.
*  A database integration test would look like this:
    1.  start a database
    2.  connect your application to the database
    3.  trigger a function within your code that writes data to the database
    4.  check that the expected data has been written to the database by reading the data from the database
*   Testing that your service integrates with a separate service via a REST API could look like this:
    1.  start your application
    2.  start an instance of the separate service (or a test double with the same interface)
    3.  trigger a function within your code that reads from the separate service's API
    4.  check that your application can parse the response correctly

*   When writing narrow integration tests you should aim to run your external dependencies locally: spin up a local MySQL database, test against a local ext4 filesystem. If you're integrating with a separate service either run an instance of that service locally or build and run a fake version that mimics the behaviour of the real service.
*   If there's no way to run a third-party service locally you should opt for running a dedicated test instance and point at this test instance when running your integration tests.
*   With regards to the test pyramid, integration tests are on a higher level than your unit tests. Integrating slow parts like filesystems and databases tends to be much slower than running unit tests with these parts stubbed out. 

### Database Integration

*   We can define H2 as a test dependency in the pom file. The application.properties in the test directory doesn't define any spring.datasource properties. This tells Spring Data to use an in-memory database.

### Integration With Separate Services

*   We want to avoid hitting the real darksky servers when running automated tests. Quota limits of our free plan are only part of the reason. The real reason is decoupling. Our tests should run independently of whatever the lovely people at darksky.net are doing. Even when your machine can't access the darksky servers or the darksky servers are down for maintenance. We can avoid hitting the real darksky servers by running our own, fake darksky server while running our integration tests.
*   Use tools like `Wiremock`.
*   It's important to understand how the test knows that it should call the fake Wiremock server instead of the real darksky API. The secret is in our application.properties file contained in src/test/resources. This is the properties file Spring loads when running tests. In this file we override configuration like API keys and URLs with values that are suitable for our testing purposes, e.g. calling the fake Wiremock server instead of the real one: `weather.url = http://localhost:8089`.
*   Writing narrow integration tests for a separate service is quite easy with tools like Wiremock. 
*   With the current implementation, the separate service could change its API and our tests would still pass. Right now we're merely testing that our WeatherClient can parse the responses that the fake server sends. That's a start but it's very brittle. Using end-to-end tests and running the tests against a test instance of the real service instead of using a fake service would solve this problem but would make us reliant on the availability of the test service. 

### Contract Tests

*   Splitting your system into many small services often means that these services need to communicate with each other via certain (hopefully well-defined, sometimes accidentally grown) interfaces.
*   Consumer-Driven Contract tests (CDC tests) let the consumers drive the implementation of a contract. Using CDC, consumers of an interface write tests that check the interface for all data they need from that interface. The consuming team then publishes these tests so that the publishing team can fetch and execute these tests easily. The providing team can now develop their API by running the CDC tests. Once all tests pass they know they have implemented everything the consuming team needs.
*   The team providing the interface should fetch and run these CDC tests continuously (in their build pipeline) to spot any breaking changes immediately. If they break the interface their CDC tests will fail, preventing breaking changes to go live.
*   Consumer-Driven Contract approach would leave you with a process looking like this:
    1.  The consuming team writes automated tests with all consumer expectations
    2.  They publish the tests for the providing team
    3.  The providing team runs the CDC tests continuously and keeps them green
    4.  Both teams talk to each other once the CDC tests break


-------------

## Load Testing Vs Performance Testing

**Load Testing** and **Performance Testing** are related but distinct types of testing used to ensure that software applications can handle expected and unexpected workloads efficiently. Here's a breakdown of each:

### **1. Load Testing**
- **Objective**: To test how a system performs under a specific expected load (number of users, requests, or transactions). The goal is to identify bottlenecks or performance degradation under normal or peak conditions.
- **Focus**: 
  - Response times
  - Throughput
  - Resource utilization (CPU, memory, disk, network)
- **Key Metrics**: 
  - Maximum concurrent users the system can handle
  - Average and peak response times
  - Error rates at specific load levels
- **Typical Scenarios**: Testing an e-commerce site with a number of users equivalent to Black Friday traffic to ensure it can handle it.
- **Outcome**: Determines if the system meets performance expectations under typical and peak loads.

### **2. Performance Testing**
- **Objective**: To evaluate the overall performance of the system, including response times, scalability, and stability under varying conditions.
- **Focus**: 
  - Speed (how fast the system performs)
  - Scalability (how well the system scales with increased load)
  - Stability (how stable the system is under sustained usage)
- **Key Metrics**:
  - Time to complete a specific task (like loading a webpage or processing a transaction)
  - Resource usage under normal conditions
- **Typical Scenarios**: Testing the system’s behavior with different loads and usage patterns to find performance limits and ensure the system performs efficiently.
- **Outcome**: Identifies performance bottlenecks and suggests improvements for better speed and efficiency.

### **Key Differences**
| **Aspect**                 | **Load Testing**                                                                                          | **Performance Testing**                                                                                   |
|----------------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| **Goal**                    | Measure performance under expected load                                                                   | Measure performance characteristics under various conditions                                                |
| **Focus Area**              | How the system handles the expected number of users or transactions                                        | Overall system performance like speed, scalability, and stability                                            |
| **Metrics**                 | Response time, throughput, and resource utilization under load                                             | Response time, resource usage, and system capacity across different loads                                    |
| **Typical Tools**           | JMeter, LoadRunner                                                                                         | JMeter, Gatling, Apache Benchmark, New Relic                                                                |
| **When to Use**             | To ensure that the system can handle expected and peak user traffic or requests                            | To measure system speed, efficiency, and scalability, even at minimal or stress conditions                   |
| **End Result**              | Helps ensure stability and reliability during high user traffic                                            | Identifies general performance improvement areas for speed and scalability                                   |

### **Other Testing Types Related to Load and Performance**:
- **Stress Testing**: Pushing the system beyond its capacity limits to see where it breaks (what happens when you exceed peak traffic).
- **Spike Testing**: Sudden bursts of load to see how the system handles sharp, unexpected increases.
- **Endurance (Soak) Testing**: Running the system under high load for an extended period to find issues like memory leaks or degradation over time.

Both load and performance testing are critical for ensuring that your application meets user expectations under real-world conditions.

