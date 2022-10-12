While splitting up some project specific code and thus adjusting tests on the unifly-asterix-tracking module, I thought I would try to run pitest on them. 
\
Just because I wanted to learn more about mutation testing.
Here’s what I have learned …

## What is pitest ?
Pitest is a mutation testing system.

## What is mutation testing ?
In a nutshell: 
A mutation test system (we will call it “the mutant”) will run your unit tests after modifying your code several times.

![image](https://user-images.githubusercontent.com/422499/195443308-2fde9ca4-8cc0-4903-9e12-bebea6d56461.png)

- If your test fails the mutant has been killed :tada: 
- If your test doesn’t fail the mutant has survived :scream: 

For example:
```java
public String greet(String name) {
   return "Hello " + name;
}
```

And you have a successful test:

```java
@Test
public void testGreeting() {
  String result = greet("John");
  assertThat(result).notNull();
  assertThat(result).contains("John");
}
```

This test has 100% coverage ! \
The mutant will probably try to run the test again by changing “Hello” to something else:
```java
public String greet(String name) {
   return "" + name;
}
```

If we would run the test above, the test will still succeed.\
This is wrong, thus your coverage is not really 100%.\

## Setup pitest
The only thing I needed to do is add the plugin to the gradle config:

```gradle
apply plugin: 'info.solidsoft.pitest'

pitest {
    junit5PluginVersion = '0.12'
    targetClasses = ['aero.unifly.*']
    outputFormats = ['XML', 'HTML']
    timestampedReports = false
}
```
After running `./gradlew` pitest there’s a nice report in build/reports/pitest/index.html:

This report indicates that the line coverage (the coverage we get from Sonar aswel) is 62%.\
After mutating the actual coverage is 28%.\
\
If you go deeper into detail you can see what pitest has been mutating:\
\
The green lines are the lines where the mutant was killed.\
The red lines are the infected by bad code coverage slime.\
\
As you can see, pitest just removed line 22 to see what happened: no tests failed.\

## Conclusion
I can hear you think: “Here we are struggling to keep up with the general line coverage, and Youri comes up with some extra coverage checks !”.\
But I do think that this can help us to write better unit tests.\
\
For me this was mainly to learn more about mutation testing, and I just used a library that was used in this tutorial: https://www.baeldung.com/java-mutation-testing-with-pitest. \
\
I know that Sonar can also do this kind of things, so it might be worth the time to think about enabling it.\
See: https://dzone.com/articles/mutation-testing-with-sonarqube
