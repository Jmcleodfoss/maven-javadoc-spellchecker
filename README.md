# maven-javadoc-spellchecker
A pom.xml file which uses the SpellCheckDocklet to spell check Javadoc from Maven. Inspired by [this answer on StackOverflow](https://stackoverflow.com/a/5458962/259686).

## Prerequisites
* Java 8 JDK, either as the default version of Java or defined in a Maven toolchain. I can recommend the [Zulu community build of OpenJDK 8](en toolchain. I have deployed the [Zulu community build of OpenJDK 8](https://www.azul.com/downloads/zulu-community/) which I have used on both Windows 10 and 32-bit CentOS.
* Maven, obviously
* [Jazzy, the Java Open Source Spell Checker](http://jazzy.sourceforge.net/). [Download the latest version from SourceForge](https://sourceforge.net/projects/jazzy/files/Jazzy/). I used [Jazzy-0.5.2](https://sourceforge.net/projects/jazzy/files/Jazzy/Jazzy-0.5.2/) for developing this.
* The Jazzy dictionaries. The pom file for checking spelling assumes these are installed inside the Jazzy installation directory in a sub-directory named dict.
** [english_dic.zip](https://sourceforge.net/projects/jazzy/files/Dictionaries/English/english_dic.zip/download)
** [english.0.zip](https://sourceforge.net/projects/jazzy/files/Dictionaries/English/english.0.zip/download)
* The [SpellCheckDoclet](http://www.softframeworks.com/etc/spellcheck/SpellCheckDoclet.html), from the [Soft Frame Works website](http://www.softframeworks.com/download/downloadFreeFile.php?file_name=spellCheckDoclet/SpellCheckDoclet.zip)

## Invoking as a separate pom file
1. Copy spellcheck-pom.xml to your project directory, and
2.  Update the properties containing the paths to Jazzy, SpellCheckDoclet, your personal ignore words list, and any project-specific ignore words list
    * spchk.doclet.path: path-to-SpellCheckDoclet
    * spchk.jazzy.path: path-to-Jazzy
    * spchk.user.ignoreFile: path-and-filename-for-user-ignore-list.txt
    * spchk.project.ignoreFile: path-and-filename-for-project-ignore-word-list.txt
3. If you are not using a user-specific or project-specific ignore word list, delete the the corresponding arguments from the javadoc plugin's additionalOptions section.
4. If you are using JDK 8 by default, delete the entire maven-toolchain-plugin plugin section.
Perform the spellcheck using
    mvn -f spellcheck-pom.xml javadoc:javadoc
The results will be written to target/site/spellcheck/CheckDoc.txt

## Adding SpellCheckDoclet to your pom file
1. Create a new profile, assumed here to be called "spellcheck", and copy the contents of the properties and build sections from
spellcheck-pom.xml into your pom file. You should omit the toolchain plugin section if your default JDK is Java 8.
2.  Update the properties containing the paths to Jazzy, SpellCheckDoclet, your personal ignore words list, and any project-specific ignore words list
    * spchk.doclet.path: path-to-SpellCheckDoclet
    * spchk.jazzy.path: path-to-Jazzy
    * spchk.user.ignoreFile: path-and-filename-for-user-ignore-list.txt
    * spchk.project.ignoreFile: path-and-filename-for-project-ignore-word-list.txt
3. If you are not using a user-specific or project-specific ignore word list, delete the the corresponding arguments from the javadoc plugin's additionalOptions section.
Perform the spellcheck using
    mvn -P spellcheck javadoc:javadoc
The results will be written to target/site/spellcheck/CheckDoc.txt

### Structure of pom file after adding spellcheck-pom.xml sections - with Java 8 toolchain for default JDK > Java 8
...
<profiles>
  <profile>
    <id>spellcheck</id>

    <properties>
      ...
    </properties>

    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-toolchains-plugin</artifactId>
          <version>3.0.0</version>
          <executions>
            <execution>
              <goals>
                <goal>toolchain</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <toolchains>
              <jdk>
                <version>1.8></version>
              </jdk>
            </toolchains>
          </configuration>
        </plugin>

        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-javadoc-plugin</artifactId>
          <version>3.1.1</version>
          <configuration>
            <doclet>spellcheck.SpellCheckDoclet</doclet>
            <docletPath>${spchk.doclet.cp}:${spchk.jazzy.cp}</docletPath>
            <echo>on</echo>
            <reportOutputDirectory>${project.reporting.outputDirectory}</reportOutputDirectory>
            <destDir>spellcheck</destDir>
            <useStandardDocletOptions>false</useStandardDocletOptions>
            <additionalOptions>...</additionalOptions>
          </configuration>
        </plugin>
      </plugins>
    </build>
  </profile>
</profiles>
...

### Structure of pom file after adding spellcheck-pom.xml sections - Java 8 as default JDK
...
<profiles>
  <profile>
    <id>spellcheck</id>

    <properties>
      ...
    </properties>

    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-javadoc-plugin</artifactId>
          <version>3.1.1</version>
          <configuration>
            <doclet>spellcheck.SpellCheckDoclet</doclet>
            <docletPath>${spchk.doclet.cp}:${spchk.jazzy.cp}</docletPath>
            <echo>on</echo>
            <reportOutputDirectory>${project.reporting.outputDirectory}</reportOutputDirectory>
            <destDir>spellcheck</destDir>
            <useStandardDocletOptions>false</useStandardDocletOptions>
            <additionalOptions>...</additionalOptions>
          </configuration>
        </plugin>
      </plugins>
    </build>
  </profile>
</profiles>
...
