---
layout: post
---

### Introduction

Bulb for JIRA is a <a href="https://www.atlassian.com/software/jira" target="blank">JIRA</a> plugin which displays information about your automated tests along with your JIRA issues. It makes test automation coverage transparent even to non-technical team members.

### Plugin REST services

#### Data import REST service

Details to use the REST service:
* Service method URL: ${JIRA_ROOT}/rest/tests/1.0/import?projectKey=${PROJECT_KEY}&tag=${REPORT_TAG}
* Service parameters:
  * projectKey - key of the JIRA project to what the test report belongs.
  * projectTag - identifier ot the test report in free form. On each import all the tests for given tag are cleared and re-imported. Therefore this is useful for projects where there are multiple categories of tests (for example unit tests and integration tests) what can't be exported in same report.
* Currently service supports only report in XML. See XSD definition of the format <a href="/files/testReportSchema.xsd">here</a>. See sample test report <a href="/files/sampleReport.xml">here</a>. If you use maven, you can use bulb maven plugin what will prepare test report in this format on build.
* Report should be posted in the HTTP request body.

#### List tests REST service method

Details to use REST service:
* Service method URL: ${JIRA_ROOT}/rest/tests/1.0/fetchTestsForProject?projectKey=${PROJECT_KEY}
* Service have only one parameter - projectKey.

**NOTE:** JIRA secures REST service methods with basic authentication - make sure you pass JIRA credentials of user with appropriate rights.

### Setup using Maven

#### Add bulb maven plugin to your project

```xml
<build>
    <plugins>
        ...
        <plugin>
            <groupId>org.bulbit.maven</groupId>
            <artifactId>bulb-maven-plugin</artifactId>
            <version>1.0.1</version>
            <configuration>
            </configuration>
            <executions>
                <execution>
                    <phase>test</phase>
                    <goals>
                        <goal>testreport</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ...
    </plugins>
</build>
```

#### Document relation between issues and tests

Start first line of test comment with prefix "Tickets: ", finish it with comma separated list of tickets to what test relates.

```java
/**
 * Tickets: ZSA-4, ZSA-1
 * Create claim
 * Go to claim detail page
 * Upload claim document to it
 * Check that email has been sent to the customer
 * @see Something
 */
@Test
public void testUpload() {
}
```

#### Run maven build

On next run of ```mvn clean install ``` maven plugin will export rests report to file trackerTestReport.xml under the target directory.

