
# Java Development Setup
The following sections are described to help you setup Java development tools on your computer:
 - [Installing Java](#installing-java)
 - [Installing Maven](#installing-maven-via-sdkman)
 - [Verify Installation](#verify-installation)
 - [Visual Studio Code Tips](#visual-studio-code-tips)

## Installing Java
There are two ways to install Java runtime/JDK on your machine:
 * Using [SDKMAN!](https://sdkman.io) - recommended and preffered for developers
 * Traditional system-wide installation - less flexible

### Installing Java Using SDKMAN!
Use [SDKMAN!](https://sdkman.io) to install and manage editions of JVMs to your home:
```sh
curl -s "https://get.sdkman.io" | bash
```

To check that SDKMAN! got installed:
```sh
sdk version
```
Also, you may want to check if SDKMAN has added these lines to your shell's .rc file in your home directory (e.g. `~/.zshrc`):
```
#THIS MUST BE AT THE END OF THE FILE FOR SDKMAN TO WORK!!!
export SDKMAN_DIR="$HOME/.sdkman"
[[ -s "$HOME/.sdkman/bin/sdkman-init.sh" ]] && source "$HOME/.sdkman/bin/sdkman-init.sh"
```

To list available editions of Java for you to install:
```sh
sdk list java
```

In the listing you get, note the identifier of a Java edition you wish to install and use it as the last parameter in the next command:
```sh
sdk install java 17.0.16-amzn
```

Finally, to set the installed version as the system-wide default you can:
```sh
sdk default java 17.0.16-amzn
```

When you need to switch from that default version to another one you have installed, in the session of the current shell:
```sh
sdk use java 25
```
#### Per-Directory Versions
With SDKMAN! you have ability to associate a directory with a specific version fo Java you installed through this command:
```sh
sdk env init
```
This will record you current Java version default in the `.sdkmanrc` file. 

The next command will use the recorded version in that file as your preferred default (and install it if not already installed) within the current directory:
```sh
sdk env install
```

You may enable a configuration option for auto-env behaviour by setting `sdkman_auto_env=true` in the `$SDKMAN_DIR/etc/config` file. This setting will automatically switch versions when stepping into a directory on the presence of a .sdkmanrc descriptor. When enabled, you no longer need to issue the install qualifier explicitly. This behaviour is disabled by default.

### Installing Java Using Package Manager or Installer (System-Wide)
> **Note**: The following instructions are for MacOS only, using the indespensible package manager `brew`.

To install the latest version of OpenJDK on MacOS:
```sh
brew install openjdk
```

To install version 17 (recomended) of OpenJDK:
```sh
brew install openjdk@17
```

Because you will likely want to prefer development with v17, ensure it is set last in you PATH like this:
```sh
echo 'export PATH="/opt/homebrew/opt/openjdk@17/bin:$PATH"' >> ~/.zshrc
```

After installing a JDK, add it to MacOS' symlink list of installed JVMs like this:
```sh
sudo ln -sfn /opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```

## Installing Maven (via SDKMAN!)
If you have SDKMAN! installed all you need to do is:
```sh
sdk install maven
```
and you are done!

## Installing Maven (System-Wide)
> **Note**: You may opt out of this part if your project includes [Maven wrapper](https://maven.apache.org/tools/wrapper/) (`./mvnw`). Your project may be using the wrapper already, which will take care of Maven install for you automatically. It is probably more worth your time to add the Maven wrapper to your project then go through the manual installation below...

As noted, the system-wide Maven installation makes sense only if for some reason you can't add the Maven wrapper to your project.

In that case, Install [Maven](https://maven.apache.org/) such that dependence of a specific version of Java is ignored. That way you won't have to use or have the latest version of Java, just so you can run Maven (which is needed as your build project manager).

```sh
brew install --ignore-dependencies maven
```

When the installation was completed, add the JAVA_HOME variable export for Maven:
```sh
echo 'export JAVA_HOME="`/usr/libexec/java_home`" #for maven' >> ~/.zshrc
```

## Verify Installation
If you installed Java and Maven **using a package manager** such as `brew` (traditional installation), you should be able to see lines similar to these in your `~/.zshrc` file:

```sh
export PATH="/opt/homebrew/opt/openjdk@17/bin:$PATH"

export NVM_DIR="$HOME/.nvm"
  [ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"  # This loads nvm
  [ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion

export JAVA_HOME="`/usr/libexec/java_home`" #for maven
```

**Regardless of which method of installing** you used, to check that Java got installed correctly, run `java --version`  from the command line. If you just finished installing the above, close and reopen terminal and then verify.
```sh
java --version
```

The output you should get should look similar to this one:
```sh
openjdk 17.0.15 2025-04-15
OpenJDK Runtime Environment Homebrew (build 17.0.15+0)
OpenJDK 64-Bit Server VM Homebrew (build 17.0.15+0, mixed mode, sharing)
```

To check for Maven system-wide installation:
```sh
mvn --version
```
If using Maven wrapper, you'd check its version inside the project directory:
```sh
cd <project_dir>
./mvnw --version
```

Output to expect:
```sh
Apache Maven 3.9.10 (5f519b97e944483d878815739f519b2eade0a91d)
Maven home: /opt/homebrew/Cellar/maven/3.9.10/libexec
Java version: 17.0.15, vendor: Homebrew, runtime: /opt/homebrew/Cellar/openjdk@17/17.0.15/libexec/openjdk.jdk/Contents/Home
Default locale: en_CA, platform encoding: UTF-8
OS name: "mac os x", version: "15.4.1", arch: "aarch64", family: "mac"
```

## Visual Studio Code Tips
You can develop in Java with ease on Visual Studio code, if you equip it with [Extension Pack for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack). For additional information of Java development setup, you can read on VS Code's [Getting Started with Java](https://code.visualstudio.com/docs/java/java-tutorial) page.

### VS Code Profiles
If you want to isolate the Java development from your other (default) development setup, you can create a separate profile first and then install the pack while under that profile. To do that, use the *File > New Window with Profile > New Profile* menu item and create.

### Remote Java Debugging
Almost from its inception, Java supported remote debugging via JDWP protocol. This allows you to create a client-server relationship between the target Java VM (client) executing some program and your debugger VM (server), typically as part of your IDE (in this case VS Code).

If you have on your debugger side the source files (JARs or classes) matching that of the program running on the target VM, which you started with a `jdwp address` option, your debugger's VM can then attach to that remote `address`. The break-points you put in the source code, will be hit before the target VM machine executes them, allowing you to inspect the contents of variables and the runtime state of the code, as it is executing.

To start Java as a JDWP target VM, you ca specify `JAVA_TOOL_OPTIONS` environment variable, before running that JVM:
```sh
# when java runs, it will read the jdwp parameters from this env var
export JAVA_TOOL_OPTIONS=-agentlib:jdwp=transport=dt_socket,address=*:8091,server=y,suspend=n
```

If you substitute `suspend=y`, the JVM starts in suspended mode and stays suspended until a debugger attaches to it. This is helpful if you want to start debugging as soon as the JVM starts. To specify the port to use when attaching the JVM to a debugger, specify `address=*:port_number`.

With target JVM running and JDWP transport established, you can now attach to that `address` port from VS Code, if you use *Attach* mode when [configuring your debugging options](https://code.visualstudio.com/docs/java/java-debugging#_configuration-options). Here is how your `./.vscode/launch.json` file would look like:
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "java",
            "name": "Attach to Remote JVM",
            "request": "attach",
            "hostName": "localhost",
            "port": 8091,
            "projectName": "myhapi"
        }
    ]
}
```

