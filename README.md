# Scoverage conflict with GenIdea

## Config
Mill version `0.11.7`

## Issue
I found an issue/conflict when I used ScoverageModule. If I add Scoverage to my project then the idea integration produces faulty configuration.
When I add Scoverage, by following the documentation: https://mill-build.com/mill/contrib/scoverage.html, the dependency between the test module and its parent module disappears :(
I was able to reproduce it with the `1-simple-scala` example. Here is the modified version:

```
import $ivy.`com.lihaoyi::mill-contrib-scoverage:$MILL_VERSION`
import mill._, scalalib._
import mill.contrib.scoverage.ScoverageModule


object foo extends RootModule with ScalaModule with ScoverageModule {
  def scalaVersion = "2.13.13"
  def scoverageVersion = "2.1.0"
  def ivyDeps = Agg(
    ivy"com.lihaoyi::scalatags:0.12.0",
    ivy"com.lihaoyi::mainargs:0.6.2"
  )

  object test extends ScoverageTests with ScalaTests {
    def ivyDeps = Agg(ivy"com.lihaoyi::utest:0.7.11")
    def testFramework = "utest.runner.Framework"
  }
}
```

After executing `mill mill.idea.GenIdea/idea` you wont find the module decalaration to the root module  in the `.idea/mill_modules/test.iml`

```
<module type="JAVA_MODULE" version="4">
    <component name="NewModuleRootManager">
        <output-test url="file://$MODULE_DIR$/../../out/test/ideaCompileOutput.dest/classes"/>
        <exclude-output/>
        <content url="file://$MODULE_DIR$/../../test">
            <sourceFolder url="file://$MODULE_DIR$/../../test/src" isTestSource="true"/>
            <sourceFolder url="file://$MODULE_DIR$/../../test/resources" type="java-test-resource"/>
        </content>
        <orderEntry type="inheritedJdk"/>
        <orderEntry type="sourceFolder" forTests="false"/>
        <orderEntry type="library" name="geny_2.13-1.0.0.jar" level="project"/>
        <orderEntry type="library" name="mainargs_2.13-0.6.2.jar" level="project"/>
        <orderEntry type="library" name="portable-scala-reflect_2.13-0.1.1.jar" level="project"/>
        <orderEntry type="library" name="scala-collection-compat_2.13-2.8.1.jar" level="project"/>
        <orderEntry type="library" name="scala-library-2.13.13.jar" level="project"/>
        <orderEntry type="library" name="scala-reflect-2.13.13.jar" level="project"/>
        <orderEntry type="library" name="scalac-scoverage-runtime_2.13-2.1.0.jar" level="project"/>
        <orderEntry type="library" name="scalatags_2.13-0.12.0.jar" level="project"/>
        <orderEntry type="library" name="sourcecode_2.13-0.3.0.jar" level="project"/>
        <orderEntry type="library" name="test-interface-1.0.jar" level="project"/>
        <orderEntry type="library" name="utest_2.13-0.7.11.jar" level="project"/>
    </component>
</module>
```

The original version of the example produces:

```
<module type="JAVA_MODULE" version="4">
    <component name="NewModuleRootManager">
        <output-test url="file://$MODULE_DIR$/../../out/test/ideaCompileOutput.dest/classes"/>
        <exclude-output/>
        <content url="file://$MODULE_DIR$/../../test">
            <sourceFolder url="file://$MODULE_DIR$/../../test/src" isTestSource="true"/>
            <sourceFolder url="file://$MODULE_DIR$/../../test/resources" type="java-test-resource"/>
        </content>
        <orderEntry type="inheritedJdk"/>
        <orderEntry type="sourceFolder" forTests="false"/>
        <orderEntry type="library" name="geny_2.13-1.0.0.jar" level="project"/>
        <orderEntry type="library" name="mainargs_2.13-0.6.2.jar" level="project"/>
        <orderEntry type="library" name="portable-scala-reflect_2.13-0.1.1.jar" level="project"/>
        <orderEntry type="library" name="scala-collection-compat_2.13-2.8.1.jar" level="project"/>
        <orderEntry type="library" name="scala-library-2.13.11.jar" level="project"/>
        <orderEntry type="library" name="scala-reflect-2.13.11.jar" level="project"/>
        <orderEntry type="library" name="scalatags_2.13-0.12.0.jar" level="project"/>
        <orderEntry type="library" name="sourcecode_2.13-0.3.0.jar" level="project"/>
        <orderEntry type="library" name="test-interface-1.0.jar" level="project"/>
        <orderEntry type="library" name="utest_2.13-0.7.11.jar" level="project"/>
        <orderEntry type="module" module-name="" exported=""/>
    </component>
</module>
```

After applying the `scoverage` configuration this line is missing:
```
<orderEntry type="module" module-name="" exported=""/>
```