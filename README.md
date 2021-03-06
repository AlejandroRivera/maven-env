maven-env
=========

**A Maven Virtual Environment, like VirtualEnv is for Python.**

This tool allows you to use different Maven versions, different global and user settings and even
different Java SDK versions per project, without changing environmental variables, while keeping a global default.

Why I made this
---------------
At my current work, our laptops are distributed with an "approved" version of Java SDK and Maven (which is a bit outdated), 
with custom global and user Maven settings. These customizations are somewhat necessary so that everyone in the company 
uses the same Maven repositories, we all use the same build profiles, etc. 

This is all fine by me, except when I want to work on personal projects where I don't want to be limited by my company's 
Maven/Java requirements.

In the past, I used to juggle different User Settings (`~/.m2/settings.xml`) and change to environmental variables 
(`M2_HOME` and `JAVA_HOME`). Sadly this approach was very error prone because I would forget to revert configurations between 
personal and work sessions. For this reason, I decided to create a tool that would automatically use the right Maven/Java 
configuration "per project" while keeping a global default.

How to use it
-------------
1. Clone this project

  ```
  git clone https://github.com/AlejandroRivera/maven-env.git
  ```
  
2. Setup aliases (for example in your `~/.bash_rc` file): 
  
  ```
  alias mvn=/path/to/maven-env/mvn
  alias mvnDebug=/path/to/maven-env/mvnDebug
  ```
3. Setup project configuration:
  
  ```
  cd /path/to/project
  mkdir .mvn                                                  # Required. Create folder that will contain maven configs
  ln -s /path/to/maven/dir           .mvn/m2_home             # Optional. Specifies which M2_HOME to use for this project. 
  ln -s /path/to/user_settings.xml   .mvn/user_settings.xml   # Optional. Determines which user settings to use (mvn -s)
  ln -s /path/to/global_settings.xml .mvn/global_settings.xml # Optional. Determines which global settings to use (mvn -gs).
  ln -s /path/to/java/dir            .mvn/java_home           # Optional. Determines which JAVA_HOME to use for this project
  ```
  Note: symlinks are recommended, but you can use regular files/directories if needed.
  
4. Done! You can now start using `mvn` and `mvnDebug` commands which will automatically use your preferred settings.

How to test it's working
------------------------
Position yourself into whatever directory you want, then execute:
```
mvn -version --debug
```
The output will show you what configuration is being used.

Caveats
-------
1. This tool only helps if you are using Maven as your build system. If you are running your Java app directly through the `java` command or deploying it to a separate web server, your project settings will be ignored and your global Java configuration will apply.
2. IDEs might not respect these settings since they might rely on the `M2_HOME` env variable or point directly to a Maven home directory. This means that you might not be able to configure them to use the aliases you created. 

Troubleshooting
---------------
**The output says the command `mvn` or `mvnDebug` cannot be found!**

If you are working on a project with `.mvn` folder, ensure the `.mvn/m2_home` is correctly pointing to a valid Apache Maven 
installation directory. A valid directory would be one that contains the `./bin/mvn` executable.

If you are outside of a project with a `.mvn` folder, this utility expects the real `mvn` executable to be found within 
your `$PATH` variable. Please ensure this is the case.

**I can no longer call `mvn` outside of projects with an `.mvn` folder. The execution crashes saying something about a fork**

Make sure you are using aliases to point to these scripts (as instructed in the "How To Use It' section) because I'm relying
on the Bash script inability to call an alias itself, thus invoking the global/default Maven executables. 

Credits
-------
Inspiration for this tool came from Python's VirtualEnv and a bit from Ruby's RVM.
Bash script was somewhat inspired from Atlassian's SDK which has a Maven wrapper (`atlas-mvn`).

License
-------
This utility is released under a MIT License. For a quick TL;DR, visit https://tldrlegal.com/license/mit-license
For the full License, see the `LICENSE` file distributed with this repository.
