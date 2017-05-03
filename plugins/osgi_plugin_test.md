# Debugging and Testing OSC Plugins

Developing and packaging an OSC plugin is a simple process however, it is also important to be able to run automated tests against the plugin code, and to debug the plugin if problems arise. This section describes how to implement unit tests, integration tests, and to run the plugin inside the Eclipse IDE using Bndtools.  

## Unit Testing
A key advantage of using Declarative Services to register components is that the plugin code can use a pure POJO programming model, and is therefore easy to unit test outside of OSGi using typical JUnit tests.  
If the plugin component references a service from the service registry, it should be injected in the test setup. For this reason it is recommended to give injection methods/fields default visibility (i.e. package visibility), and to declare the unit tests for the component in the same package as the component. The separation between the main source and test source directories will ensure that the test code is not packaged into the bundle.  
When a component does reference other services, it is usually best to inject a mock object so that interactions can be stubbed or verified as needed. Common mocking frameworks such as Mockito, PowerMock, EasyMock etc. are all suitable. The following test class sets up a component ready for testing in one of its test methods: 

```java
@RunWith(MockitoJunitRunner.class)
public class ExampleApplianceManagerTest
{
    @Mock
    MyServiceDependency mockedDependency;
    ExampleApplianceManager underTest;
    
	@Before
    public void setUp() {
        underTest = new ExampleApplianceManager();
        underTest.setMyServiceDependency(mockedDependency);
        underTest.start();
    }
	
    @Test
    public void testApplianceManager() {
        // Use underTest to check its behaviour
    }
}
```

## OSGi Integration Testing
OSGi integration testing differs from unit testing, in that it occurs inside an OSGi framework. OSGi integration testing is used to validate that bundles interact correctly with the OSGi framework, and with other OSGi bundles and services.  
OSGi integration tests differ from unit tests, in that they do not normally test low level functionality. Integration tests typically perform high-level verification of functions, for example validating that expected services are registered and retrievable. Integration tests are typically defined in a separate Maven project from the bundle they are testing. There are two main ways to run OSGi integration tests:

* OSGi testing using the `bnd-testing-maven-plugin'
* OSGi testing using PAX Exam

### OSGi Testing Using The bnd-testing-maven-plugin
Bndtools and the bnd library have offered in framework integration tests using JUnit for many years however, these features were only available in Ant and Gradle builds. In the latest SNAPSHOT releases, bnd has added Maven integration testing support with the `bnd-testingmaven-plugin`.  
Bnd integration tests look like normal JUnit tests, but are built and packaged into an OSGi bundle, meaning that they are compiled from `src/main/java`. Typically they are packaged using the `bnd-maven-plugin`, and they declare the `Test-Cases` header, which lists the classes containing tests. The value of this header can be auto generated using a macro to select the files in the project’s bnd.bnd file.  

**Selecting Classes with Names Ending in Test**
```
Test-Cases: ${classes;named;*Test}
```

**Selecting Classes Annotated with Junit’s RunWith**
```
Test-Cases: ${classes;annotated;org.junit.runner.RunWith}
```

The test class does not need to follow any special rules, and can use OSGi’s FrameworkUtil class to obtain its Bundle Context. It is then easy to interact with the OSGi framework.  
```java
public class ExampleApplianceManagerIntegrationTest
{
    BundleContext context = FrameworkUtil.getBundle(ExampleApplianceManagerIntegrationTest.class).getBundleContext();

    @Before
    public void setUp() {
        // Runs before each test, but inside OSGi!
    }

    @Test
    public void testApplianceManager() {
        // Runs like a normal JUnit test, but inside OSGi!
    }
}
```

Once a set of test cases has been identified, it is necessary to configure the `bnd-testingmaven-plugin`. The `bnd-testing-maven-plugin` is added to the Maven build just like any other Maven plugin:
```xml
<plugin>
    <groupId>biz.aQute.bnd</groupId>
    <artifactId>bnd-testing-maven-plugin</artifactId>
    <version>3.4.0-SNAPSHOT</version>
    <configuration>
        <bndruns>
            <bndrun>test.bndrun</bndrun>
        </bndruns>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>testing</goal>
            </goals>
        <execution>
    </executions>
</plugin>
```

You may have already noticed that the plugin configuration points to a bndrun file. A bndrun file is a description that can be used to launch an OSGi framework. Bndrun files also support the automatic resolution of bundle dependencies based on a set of run requirements.  
When using Maven a bndrun file, it needs to be set up in `-standalone` mode using the `-standalone` instruction. This instruction can be used to point at one or more OSGi repository XML indexes, which will be used for resolution. In addition to standard XML repositories, bnd also supports using a variety of other repositories via its plugin model. When running in Maven, it can be very useful to use the local POM file as a repository using the BndPomRepository.  
```
-standalone:
-plugin.testing = \
aQute.bnd.repository.maven.pom.provider.BndPomRepository; \
snapshotUrls=https://oss.sonatype.org/content/repositories/osgi/; \
releaseUrls=https://repo1.maven.org/maven2/; \
pom=${.}/pom.xml; \
name=testing; \
location=${.}/target/cached.xml
```

Once the standalone bndrun file’s repository has been created, the Bndtools UI can be used to automatically populate information such as:  

* The OSGi framework that should be used.
* The Test’s Run Requirements.
* The resolved set of Run Bundles.

Note that the bundles available to the bndrun file are determined by the POM dependencies. At a minimum, these will need to include an OSGi framework such as:  
```xml
<dependency>
    <groupId>org.apache.felix</groupId>
    <artifactId>org.apache.felix.framework</artifactId>
    <version>5.4.0</version>
    <scope>test</scope>
</dependency>
```

Once the bndrun file is populated, the tests can be run as normal Maven tests.  
If Bndtools is not available, the `bnd-testing-maven-plugin` is also able to resolve the bndrun file by setting `<resolve>true</resolve>` in its configuration. The raw bndrun file should look something like the following:
```
-runrequires: \
osgi.identity;filter:=‘(osgi.identity=org.osc.example.examplemanager-test)’ \
osgi.identity;filter:=‘(osgi.identity=org.osc.example.examplemanager-impl)’
-runfw: org.apache.felix.framework;version=‘[5.4.0,5.4.0]’
-runee: JavaSE-1.8
-resolve.effective: resolve, active
```

The bnd-testing-maven-plugin will then output a failure indicating that the run bundles have changed, including the resolved list of run bundles. These can be added to the bndrun file using the -runbundles instruction.  

### OSGi Testing Using PAX-Exam
PAX-Exam is a long-established tool for running OSGi integration tests as part of a Maven build.  
PAX-Exam tests use a special JUnit 4 Runner to launch an OSGi framework and install bundles into it. The test class is then packaged into a “probe” bundle which is also installed into the OSGi framework. The JUnit tests are then run one by one.  
PAX-Exam requires the following Maven dependencies:  
```xml
<dependency>
    <groupId>org.apache.felix</groupId>
    <artifactId>org.apache.felix.framework</artifactId>
    <version>5.4.0</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.ops4j.pax.exam</groupId>
    <artifactId>pax-exam-junit4</artifactId>
    <version>4.9.1</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.ops4j.pax.exam</groupId>
    <artifactId>pax-exam-container-forked</artifactId>
    <version>4.9.1</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.ops4j.pax.exam</groupId>
    <artifactId>pax-exam-container-forked</artifactId>
    <version>4.9.1</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.ops4j.pax.exam</groupId>
    <artifactId>pax-exam-link-mvn</artifactId>
    <version>4.9.1</version>
    <scope>test</scope>
</dependency>
```

An integration test is then written using the PAX-Exam runner, and defining one or more Configurations which returns a list of the bundles to be installed, and other startup options to be applied to the OSGi framework. Bundles are typically installed based on the Maven dependencies of the test project. OSGi services, and resources such as a `BundleContext` can be directly injected into the test class using `@Inject`. Service injection can be further restricted using an LDAP filter to select a service with particular properties.

```java
@ExamReactorStrategy(PerMethod.class)
public class ExampleApplianceManagerIntegrationTest
{
    @Inject
    @Filter(“(osc.plugin.name=Example)”)
    ApplianceManagerApi underTest
    @Configuration
    public Option[] configure() {
        // Runs outside the OSGi framework
        return CoreOptions.options(mavenBundle(“org.osc.example”, “example-manager-impl”).versionAsInProject(), /*…*/);
    }

    @Before
    public void setUp() {
        // Runs before each test, but inside OSGi!
    }
	
    @Test
    public void testApplianceManager() {
        // Runs like a normal JUnit test, but inside OSGi!
    }
}
```

### Comparing the `bnd-testing-maven-plugin`  and Pax Exam  
Both the `bnd-testing-maven-plugin` and Pax Exam are powerful tools for running OSGi integration tests in Maven, and either can be used successfully as part of an automated testing infrastructure. There are however, some significant advantages and disadvantages to each approach.  
The `bnd-testing-maven-plugin` is comparatively new, and does not have a formal release yet however, it is based on a long-standing test runner from the bnd project, and has support in existing versions of Bndtools. Bnd also has the advantage that it supports dependency resolving.  
This can be used to ensure that all of the bundle metadata is correct, and it avoids having to manually maintain a list of dependencies. Finally, because bnd separates out the framework launch configuration into a separate bndrun file, the test code is cleaner and easier to read. 
Pam Exam differs from bnd in that it provides no resolving step, and it requires all of the OSGi framework configuration to happen inside the test class. This has the advantage that there is only one file to change in order to change the tests however, it also means that it can be easy to lose track of the tests amongst the startup boilerplate code. Despite its more verbose setup, Pax Exam provides useful dependency injection, stability, and is widely used.  

## Debugging OSGi Code Using Bndtools
In addition to automated testing, bnd and Bndtools offer a rich environment for interactive debugging using bndrun files. A bndrun file contains a definition for launching an OSGi framework.  
This definition includes:

* A list of repositories in which bundles can be found.
* The OSGi framework that should be used.
* A list of run requirements which selects bundles from the repositories.
* The complete list of bundles to run which can be auto-generated from the run requirements.

There are two main options for defining repositories in a plugin build:

**Using the Maven POM File**
```
-standalone:
-plugin.testing = \
aQute.bnd.repository.maven.pom.provider.BndPomRepository; \
snapshotUrls=https://oss.sonatype.org/content/repositories/osgi/; \
releaseUrls=https://repo1.maven.org/maven2/; \
pom=${.}/pom.xml; \
name=Running; \
location=${.}/target/cached.xml
```

**Using a Generated Plugin Index**
```
-standalone: ${.}/target/plugin/index.xml;name=Running
```

> Note: The Bndtools UI can provide help when referencing a generated index.  

Once the repositories that you want to use have been defined, an OSGi framework and Java platform version can be selected using the Bndtools UI:
```
-runfw: org.apache.felix.framework;version=‘[5.4.0,5.4.0]’
-runee: JavaSE-1.8
```

The next step is to add requirements for the bundles that you want to run. Again, the Bndtools UI can help here as bundles can simply be dragged and dropped into the run requirements panel.  
```
-runrequires: \
osgi.identity;filter:=‘(osgi.identity=org.osc.example.examplemanager-impl)’
```

Before resolving, it is also a good idea to enable active time resolution. This tells the Bndtools resolver to include requirements that don’t affect the OSGi resolver, but do indicate loosely coupled dependencies such as OSGi service dependencies.
```
-resolve.effective: resolve, active
```

The Bndtools resolve operation will then validate that the included repositories include the right dependencies to allow the application to run. If so, a list of run bundles is generated and added to the bndrun file. Once a list of run bundles has been added, the bndrun can be launched inside the Bndtools IDE.

### Additional Utilities
When running inside the IDE, it can be useful to include some additional run requirements for debugging tools. These can be separated from the main run requirements by adding them as a second set of run requirements with a suffix.
```
-runrequires.debug: \
osgi.identity;filter:=‘(osgi.identity=org.apache.felix.gogo.shell)’
```

Tools such as the GoGo shell and the Apache Felix Web Console can provide a simple way to introspect and interact with the code running in the IDE. As such, they are well worth investigating for any OSGi developer.