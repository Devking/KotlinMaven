Starting Kotlin with Maven
==========================

This repository gives a basic template for working with Kotlin using Maven.

## Introduction for Beginners

This tutorial assumes you have some basic familiarity with the command line. It
does not assume you have used Kotlin, nor Maven, before.

[Kotlin](http://kotlinlang.org/) is a programming language developed by the
folks over at JetBrains, the same company that developed the IntelliJ IDEA
IDE for Java. Kotlin is nice because it is interoperable with Java, meaning
that you can use existing Java libraries with your Kotlin code.

[Maven](http://maven.apache.org/) is a software project management tool that
helps to build, test, and document your software projects.

One approach to writing Kotlin programs is to get
[IntelliJ IDEA](https://www.jetbrains.com/idea/), which ships with Kotlin.
However, if you prefer to work on the command line, but have a large Kotlin
project with many different components and dependencies, you may wish instead
to use Maven. This repository steps through the basics of how to get Maven
set up for Kotlin development.

## Getting Maven

The first thing you need to do is install Maven. You can do this a variety of
ways, but below are two very easy and common ways to do so:

Using Homebrew (Mac):
```bash
brew install maven
```

Using apt-get (Ubuntu):
```bash
sudo apt-get install maven
```

Alternately, you can go to the Apache website to
[download Maven](https://maven.apache.org/download.cgi) and
[install](https://maven.apache.org/download.cgi#Installation) it manually.

## Creating the Maven Project

The [Maven in 5 Minutes](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)
guide steps through this process quite nicely, but I'll reproduce the relevant
steps here, since we'll need to do some additional work to get Maven to work
nicely with Kotlin.

The first thing you should do is navigate to the directory where you want to
set up your project. Next, you'll want to run:

```bash
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

replacing `com.mycompany.app` and `my-app` with a more appropriate group and
artifact id, if you wish. We can change these later, as well, so it's not too
critical that you decide on good names now.

This command (what they call a *Maven goal*) will create a directory for your
project, with the name that you have specified for your artifact id. Once it's
generated, navigate to that directory. In my case, I've called the artifact id
as `tutorial`, and you can find the `tutorial` project directory I've generated
in this repository.

It's good to familiarize yourself with the project structure that has been
created within this directory. The most important thing to take note of is the
`pom.xml` file that has been created. This file, which is often referred to as
the POM, specifies the configuration information for Maven to build your project
with the dependencies, versions, target files, etc. that you want. At first
glance, this file will look very complicated, but as you work with the POM and
Maven more, it will become less daunting.

## Configuring the POM for Kotlin

Right now the POM is configured to build for Java. You'll need to manually
configure the POM in order to get it working for Kotlin. We'll also add some
configurations to make running your code a bit nicer. You can find more
information on this step on the Kotlin website's official tutorial docs for
[Using Maven](http://kotlinlang.org/docs/reference/using-maven.html).

### XML

First, open up `pom.xml`. For this tutorial, I assume that you have basic
familiarity reading an XML file, but [here's a helpful video](https://www.coursera.org/learn/data-cleaning/lecture/cieIu/reading-xml)
on XML if you wish to learn more about it. The gist of XML is that it is a way
to structure data in a uniform format, so that many different applications can
easily engage with the information given in an XML file. It uses "tags" to
label your information. For instance, look at the following line in the POM:

```
<groupId>com.wellssanto.tutorials</groupId>
```

This line specifies that the `groupId` is set to `com.wellssanto.tutorials` in
my case. This is what I specified my group id to be when running the command
in the previous section. The opening tag specifies that everything after it will
describe the label `groupId`, and the closing tag (notice the `/` at the start
of it) specifies that we've reached the end of the description of `groupId`.

### Adding Kotlin Dependencies to the POM

To use Kotlin, we'll need to add a dependency and some build plugins.

Within the `dependencies` tags, you'll notice that one `dependency` already
exists for `junit`. This is for running unit tests over your project. Don't
worry about this for now. Let's add our Kotlin dependency, by pasting the
following between the exisiting `<dependencies>` and `<dependency>` tags:

```
<dependency>
	<groupId>org.jetbrains.kotlin</groupId>
	<artifactId>kotlin-stdlib</artifactId>
	<version>${kotlin.version}</version>
</dependency>
```

Notice that by doing this, we have added one dependency to our list of
dependencies. Be sure to follow good indentation practices and indent what we've
just copied into the POM so that it lines up with the existing `junit`
dependency description. Adding white space between lines (after a close tag
and before an open tag) won't affect the POM. It will, however, help with
readability.

Optionally, we may also wish to add another dependency for Kotlin tests:

```
<dependency>
	<groupId>org.jetbrains.kotlin</groupId>
	<artifactId>kotlin-test-junit</artifactId>
	<version>${kotlin.version}</version>
	<scope>test</scope>
</dependency>
```

### Adding version properties to the POM

Notice that both of the dependencies we have added above make reference to
`${kotlin.version}`. We haven't specified what version of Kotlin we want to use,
so let's do that. After the close tag for `url`, and before the open tag for
`dependency`, add the following:

```
<properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	<kotlin.version>1.0.3</kotlin.version>
	<junit.version>4.12</junit.version>
</properties>
```

This specifies some properties for our project, such as the Kotlin version to use,
the junit version to use, and the type of source encoding we wish to use. For
now, I am using the versions listed above, which are the latest versions at the
current moment that this tutorial has been written. You can change the versions
listed to reflect whatever other versions you wish to use.

Note also that I specified a junit version here, but a junit version was already
specified in the junit dependency. Let's go and modify that by replacing:

```
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>3.8.1</version>
	<scope>test</scope>
</dependency>
```

with

```
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>${junit.version}</version>
	<scope>test</scope>
</dependency>
```

### Adding Build Description

Add the following after the close tag for `dependency` and before the close tag
for `project`:

```
<build>
	<sourceDirectory>${project.basedir}/src/main/kotlin</sourceDirectory>

	<plugins>
		<plugin>
			<artifactId>kotlin-maven-plugin</artifactId>
			<groupId>org.jetbrains.kotlin</groupId>
			<version>${kotlin.version}</version>
			<executions>
				<execution>
					<id>compile</id>
					<goals> <goal>compile</goal> </goals>
				</execution>
				<execution>
					<id>test-compile</id>
					<goals> <goal>test-compile</goal> </goals>
				</execution>
			</executions>
		</plugin>

		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-assembly-plugin</artifactId>
			<version>2.2-beta-4</version>
			<configuration>
				<finalName>runtutorial</finalName>
				<appendAssemblyId>false</appendAssemblyId>
				<descriptorRefs>
					<descriptorRef>jar-with-dependencies</descriptorRef>
				</descriptorRefs>
				<archive>
					<manifest>
						<mainClass>HelloKt</mainClass>
					</manifest>
				</archive>
			</configuration>
			<executions>
				<execution>
					<phase>package</phase>
					<goals>
					<goal>single</goal>
					</goals>
				</execution>
			</executions>
		</plugin>

	</plugins>
</build>
```

Without getting into too much detail into what was added, notice that we have
added two plugins and specified a source directory where we are building from.
That is, we have specified that when we use Maven to build our project (at a
later step), we will look the specified directory for the project code. We will
create that directory and add a Kotlin file to it in the next step.

We will revisit this chunk of information at a later step. The POM is now all
set up and ready to go for your first Kotlin project! You can take a look at the
POM found within the `tutorial` directory within this repository to see that
you've done everything correctly.

## Writing Your First Kotlin Program

### Creating a Directory for your Kotlin Code

Before we write our first Kotlin program, we need to make sure to put it in the
correct directory. Currently, that directory does not yet exist in our project.
Let's make it now. Assuming you are on the command line and in the same directory
where the POM is, run the following:

```bash
cd src/main/
mkdir kotlin
```

This will create the `src/main/kotlin` directory that we've specified as the
source directory in our POM in the previous step. Now, navigate into that
directory and create a new Kotlin file, called `Hello.kt` (yes, the name
matters).

### Hello World in Kotlin

We're going to do the very boring thing and write "Hello World" in Kotlin.

In `Hello.kt`, paste the following:

```kotlin
fun main (args: Array<String>) {
	println("Hello world!")
}
```

Notice how much shorter this "Hello World" program is than what it would be in
Java. You can read more about the Kotlin language and its syntax on the Kotlin
website's [References](http://kotlinlang.org/docs/reference/) page.

## Building and Running

A quick re-cap of what we've done:
1. We've installed Maven
2. Used Maven to generate a generic project
3. Modified the POM to work with Kotlin
4. Wrote a Kotlin program

The next step is to actually use Maven to compile and build our project, and
then run it afterwards. Navigate back to the directory with the POM, and run
the following command to compile and build:

```bash
mvn package
```

This should compile and build your project successfully. If it does not, be sure
to go over the previous steps again and see if there's anything that you've
missed. You can also look at both `pom.xml` and `Hello.kt` in this directory to
see that everything matches up.

Once the build is complete, you will notice that a new directory called `target`
has been created. The command that we have just run takes the compiled Kotlin
code and packages it up into a JAR, which can be distributed across machines
and run. If you look inside the `target` directory, you will notice that there
exists `runtutorial.jar`, which we will run in the normal Java way, as follows:

```bash
java -jar target/runtutorial.jar
```

Upon running, you should get the output:

```
Hello world!
```

which is exactly as we'd expect. You can now go back and modify your Kotlin code
as you please to continue developing your first Kotlin project using Maven. Be
sure to run `mvn package` whenever you wish to compile and build a new JAR to
run.

## Revisiting the POM

### Looking Back at the Build Description

In a previous section, we hand-waved a chunk of information that was added to
the POM to specify the build description. Let's take a quick look at that now.

First, take notice to the `finalName` label within the `configuration` for the
second plugin. Notice that it has been specified to `runtutorial`, which is the
name of our JAR file. You can change this to anything else that you wish your
JAR file to be named.

Next, notice the `mainClass` label nested within the `manifest` and `archive`
tags, also in the second plugin section. The main class is specified as
`HelloKt`, which corresponds to the `Hello.Kt` file we have written. This
specifies the entry point for your project (aka, which `main` will be run), and
you can change this as you wish. For example, if you wish the entry point for
your project to be in `init.kt`, then your `mainClass` label should point to
`initKt`.

I may add explanations of other pieces of the build description later.

### Adding Third-Party Java and Kotlin Libraries

If a library exists for Java or Kotlin and is on
[Maven Central](http://search.maven.org/), you can add it to your project
quite easily.

For example, if I wished to add the [BURLAP](http://burlap.cs.brown.edu/)
Reinforcement Learning library to my project, all I would have to do is add the
following dependency to my POM:

```
<dependency>
  <groupId>edu.brown.cs.burlap</groupId>
  <artifactId>burlap</artifactId>
  <version>3.0.0</version>
</dependency>
```

You can then use `import` as you normally would.
