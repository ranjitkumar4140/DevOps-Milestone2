# DevOps-Milestone2: Test and Analysis

Team Members:

1. Ayush Gupta - agupta25@ncsu.edu
2. Nishtha Garg - ngarg@ncsu.edu
3. Shivam Gulati - sgulati2@ncsu.edu

We have used Jenkins to configure the build server. Alongwith Jenkins, we have used Tomcat, Git for Source Code Management and NPM as a package manager.

We used the MIT licensed Climbing-Grade project which is forked at 
```
https://github.com/shivamgulati1991/climbing-grade
```

#### Jenkins Job Creation Steps


1. Create a new job. Give it a name. Choose Freestyle Project.
2. For configuration of Job
   Source Code Management-> select Git under Source Code Management-> Enter your project's Git Repository URL.
3. Under Build Environment-> select "Provide Node & npm bin/ folder to PATH"-> choose the NodeJS installation.
4. Additionally, you can also setup multiple branches for the same job.
5. Click Save.
5. Test run a build - Go to Jobs and click "Build Now". 

### Test Section

#### Test Suites: The ability to run unit tests, measure coverage, and report the results.

We used Istanbul code coverage with tests and Cobertura Jenkins plugin to display the report.

1. Go to Manage Jenkins->Manage Plugin->Install Cobertura plugin.
2. Go to your Job -> Configure-> Build-> Execute shell, write this code.

```
rm -rf node_modules
npm install
npm test
```

Calling npm test calls the test suit, generates the report using istanbul and writes it into cobertura-coverage.xml. This report is fed to Cobertura Jenkins plugin for display. We used '.istanbul.yml' file for it to generate cobertura-coverage.xml file

3. In Post-build section, select Publish Cobertura Coverage Report and give this report path which is generated by istanbul

```
**/coverage/cobertura-coverage.xml
```

4. Click Save.
5. Build the job
   
![Screencast](https://github.com/shivamgulati1991/DevOps-Milestone2/blob/master/Screens/1.gif)


#### Advanced Testing: Implement one of the following advanced testing techniques: test priorization, test case generation, fuzzing, or flaky test quarantine.

For this step, we implemented the constraint test case generation method similar to the coursework workshop and homeworks.

1. In our main.js file, we wrote the code for test case generation and added the contraints.
2. This file was fed to instanbul for code coverage and consumed by Cobertura Jenkins plugin similar to the step one.
3. In Jenking job -> Build -> Execute shell

```
rm -rf node_modules
npm install
node main.js
npm run customtest

```

4. 'node main.js' runs the test case generation and generates 'test.js' file which is run as per 'npm run customtest'. The 'package.json' file has the reference for the same. Istanbul generated the coverage and Cobertura plugin uses the same to display in Jenkins.

![Screencast](https://github.com/shivamgulati1991/DevOps-Milestone2/blob/master/Screens/2.gif)

### Analysis Task

#### Basic Analysis: The ability to run an existing static analysis tool on the source code (e.g. FindBugs, PMD, CheckStyle, NCover, Lint, etc.), process its results, and report its findings.

We used eslint for basic analysis on our source code. eslint performs static analysis and outputs its results. We also used CheckStyle Jenkins plugin to display the same in Jenkins.

1. Go to Manage Jenkins->Manage Plugin->Install CheckStyle plugin.
2. Go to your Job -> Configure-> Build-> Execute shell, write this code.

```
rm -rf node_modules
npm install
npm test
npm run lint || :
```

3. In your configure -> Post-build section, select Publish CheckStyle Analysis Results and leave the path blank so it takes the default on its own.
4. Build your job. While building eslint will perform static analysis on the code and output the results and warnings to Jenkins.

![Screencast](https://github.com/shivamgulati1991/DevOps-Milestone2/blob/master/Screens/3.gif)


#### Custom Metrics: The ability to implement your own custom source metrics.

##### Max condition: Count the max number of conditions within an if statement in a function.

As taught in complexity class workshop, we used the same approach and extended the code to count the conditions and report the output.

##### Long method: Detect a long methods.

As taught in complexity class workshop, we used the same approach and extended the code to count the conditions and report the output. We decided 30 lines as a threshold to detect long methods for this, and any method above that results in the output with the lines of code it contains.

##### Free-style: Implement any analysis, such as security-token detection.

Since AWS and Digital Ocean and the primary providers we have used so far, we used regular expressions to generate the pattern for security token for both of them. We wrote the script detectToken.js which checks for the same and reports the output if there is a token present in the code.

##### BONUS (10 points): Detect duplicate code using an AST-based difference algorithm.


![Screencast](https://github.com/shivamgulati1991/DevOps-Milestone1/blob/master/Screens/4.gif)


#### Gates: Using hooks or post-build scripts, have the ability to reject a commit if it fails a minimum testing criteria (e.g. failed test case, or less than 50% statement coverage) and analysis criteria (e.g. cannot commits that generate a particular FindBugs rule, such as "Method concatenates strings using + in a loop").

We wrote a git hook which on commit, performs testing and analysis on the code.

1. If the test cases fail, the commit is rejected.
2. For the analysis, we used the eslint rules to especially check for 'no-unreachable' code area, basically if the code has unreachable code part, it is detected and the commit is rejected. All rules are defined in file '.eslintrc.js'
3. If both testing criteria and analysis criteria does not result in any error, the commit is successful.


Screencast for rejecting commit with failing test case

![Screencast](https://github.com/shivamgulati1991/DevOps-Milestone2/blob/master/Screens/5_fail1.gif)

Screencast for rejecting commit with analysis

![Screencast](https://github.com/shivamgulati1991/DevOps-Milestone2/blob/master/Screens/5_fail2.gif)

Screencast for a successful commit after both testing and analysis pass

![Screencast](https://github.com/shivamgulati1991/DevOps-Milestone2/blob/master/Screens/5_pass.gif)


Sources:

1. https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins
2. https://github.com/Grantismo/climbing-grade.js
