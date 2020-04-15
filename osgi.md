## TL;DR

### Article Link
https://github.com/clojusc/osgi/
https://gitlab.com/refarch/reference/-/commit/9a06acd6a7beab0735549f86a4b935961f5d6146
http://wiki.obrienlabs.cloud/display/DEV/Kubernetes+Developer+Guide
github.com/yamad/jaxrs-docker-example/
https://github.com/klarna/brucke
https://sites.google.com/site/gettingalongwitherlang/home/misc/communication-with-java/Erlang-RPC-implementation-by-Java
https://github.com/J3T4R0/storefront-demo-cases

## Key Takeaways
* How to expose an api through docker and connect to it through spawned processes in a maintaiable way
* Using RPC

## Useful Code Snippets
```xml
<?xml version="1.0" encoding="Shift_JIS" ?>

<project name="WorkingWithErlang" default="jar">

  <property name="jar.name" value="MyRPCClient.jar"/>
  <property name="erlang.home" value="d:/erl5.7.4"/>
  <property name="src.dir" value="./src"/>
  <property name="classes.dir" value="./classes"/>
  <property name="javadoc.dir" value="./docs"/>
  <property name="lib.dir" value="./lib"/>

  <!-- Library path of Erlang OTP for Java -->
  <path id="erlang.classpath">
    <fileset dir="${erlang.home}/lib/">
    <include name="**/*.jar" />
    </fileset>
  </path>

  <!-- other 3rd-party libraries that are used for this application if required -->
  <path id="my.classpath">
    <fileset dir="${lib.dir}">
      <include name="**/*.jar" />
    </fileset>
  </path>

  <target name="jar" depends="compile">
    <jar jarfile="${jar.name}" basedir="${classes.dir}"></jar>
  </target>

  <target name="compile" depends="prepare">
    <javac  srcdir="${src.dir}" destdir="${classes.dir}" includes="**/*.java">
        <classpath refid="my.classpath" />
        <classpath refid="erlang.classpath" />
    </javac>
  </target>

  <target name="prepare" description="create build directory for compiler output">
    <mkdir dir="${src.dir}"/>
    <mkdir dir="${classes.dir}"/>
    <mkdir dir="${lib.dir}"/>
  </target>

  <target name="clean">
    <delete includeemptydirs="true">
      <fileset dir="${classes.dir}" includes="**/*"/>
    </delete>
    <delete file="${jar.name}" />
  </target>

</project>
```
Main.java
```java

package com.ykishi.rpctest;

import com.ericsson.otp.erlang.*;

public class Main {

    public static void main ( String[] _args ) throws Exception {

        OtpSelf cNode = new OtpSelf ( "client", "theCookie" );
        OtpPeer sNode = new OtpPeer ( "server@ragtime-8192" );
        OtpConnection connection = cNode.connect ( sNode );

        OtpErlangObject[] args = new OtpErlangObject[] {
            new OtpErlangLong ( 1 ), new OtpErlangLong ( 2 )
        };
        connection.sendRPC ( "math_server", "add", args );
        OtpErlangLong sum = ( OtpErlangLong ) connection.receiveRPC();

        System.out.println ( sum.intValue() );

    }

}
```
Rpc-client.sh
```sh
:::::::::::::
rpc-client.sh
::::::::::::::
#!/bin/sh

CLASSPATH=D:\\erl5.7.4\\lib\\jinterface-1.5.2\\priv\\OtpErlang.jar

java -cp ${CLASSPATH}\;./MyRPCClient.jar com.ykishi.rpctest.Main
::::::::::::::
Makefile
::::::::::::::
# $Id: Makefile,v 1.1 2009/12/26 09:13:30 kishi Exp kishi $

.SUFFIXES: .erl .beam
.erl.beam:
        erlc $<

SOURCES = $(wildcard *.erl)
BEAMS = $(wildcard *.beam)
PROGS = $(SOURCES:.erl=.beam)

all: $(PROGS)

clean:
        rm -f $(BEAMS)
```
## Useful Tools
* RPC 
* OSGI
* Ewarp
* Jinterface
* Brucke
* Kafka

## Comments/ Questions
