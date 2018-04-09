# mvn-thrift-compiler

Pre-compiled Apache Thrift binaries distribution for Maven projects.
Helpful when trying to auto-generate Thrift sources as part of a Maven build without having Thrift installed in the system.

- Thrift version: 0.9.3
- OS and arch supported: linux-x86_64, osx-x86_64, windows-x86_64

## Example usage

See [example/pom.xml](example/pom.xml) for a full working example

Used in combination with https://github.com/dtrott/maven-thrift-plugin


      <project>
      	...
      	<properties>
      		<thrift.path>${project.build.directory}/thrift-compiler/</thrift.path>
      		<thrift.filename>thrift-${os.detected.classifier}.exe</thrift.filename>
      	</properties>

      	<repositories>
      		<!-- Needed for dependencies hosted on GitHub (like this one) -->
      		<repository>
      			<id>jitpack.io</id>
      			<url>https://jitpack.io</url>
      		</repository>
      	</repositories>

      	<build>
      		<extensions>
      			<extension>
      				<groupId>kr.motd.maven</groupId>
      				<artifactId>os-maven-plugin</artifactId>
      				<version>1.5.0.Final</version>
      			</extension>
      		</extensions>

      		<plugins>
      			<!-- Download and extract thrift executable -->
      			<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <version>3.1.0</version>
                <executions>
                    <execution>
                        <id>unpack</id>
                        <phase>initialize</phase>
                        <goals>
                            <goal>unpack</goal>
                        </goals>
                        <configuration>
                            <artifactItems>
                                <artifactItem>
                                    <groupId>com.github.ccascone</groupId>
                                    <artifactId>mvn-thrift-compiler</artifactId>
                                    <version>1.2_0.9.3</version>
                                    <type>jar</type>
                                    <includes>thrift-${os.detected.classifier}.exe</includes>
                                    <outputDirectory>${project.build.directory}/thrift-compiler</outputDirectory>
                                </artifactItem>
                            </artifactItems>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.6.0</version>
                <executions>
                    <execution>
                        <id>script-chmod</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>exec</goal>
                        </goals>
                        <configuration>
                            <executable>chmod</executable>
                            <arguments>
                                <argument>+x</argument>
                                <argument>${project.build.directory}/thrift-compiler/thrift-${os.detected.classifier}.exe</argument>
                            </arguments>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <!-- Compile Thrift files -->
            <plugin>
                <groupId>org.apache.thrift.tools</groupId>
                <artifactId>maven-thrift-plugin</artifactId>
                <version>0.1.11</version>
                <configuration>
                    <thriftExecutable>${project.build.directory}/thrift-compiler/thrift-${os.detected.classifier}.exe</thriftExecutable>
                </configuration>
                <executions>
                    <execution>
                        <id>thrift-sources</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>compile</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <!-- Make generated sources visible -->
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>build-helper-maven-plugin</artifactId>
                <version>1.4</version>
                <executions>
                    <execution>
                        <id>add-thrift-sources-to-path</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>add-source</goal>
                        </goals>
                        <configuration>
                            <sources>
                                <source>
                                    ${project.build.directory}/target/generated-sources/thrift
                                </source>
                            </sources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

      		</plugins>

      	</build>
        ...
      </project>
