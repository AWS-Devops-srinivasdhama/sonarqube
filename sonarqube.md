Below are your complete SonarQube notes in Markdown format, directly in the chat so you can copy them into VS Code, Notepad, GitHub README, or OneNote.

I have organized the headings, commands, tables, and examples while retaining your original topics. Exposed SonarQube tokens and passwords are redacted for security.

# SonarQube Notes

## 1. Introduction to SonarQube

Before Maven and Tomcat → SonarQube.

- SonarQube is an open-source platform used for continuous inspection of code quality.
- It performs static code analysis to identify bugs, vulnerabilities, and code smells in your code.

### Key Features

1\. Code Quality and Security

- Bugs and Vulnerabilities → Detect and fix issues that might lead to bugs or security vulnerabilities.
- Code Smells → Identify maintainability issues in your code.
- Code Coverage → Measure how much of your code is covered by tests.
- Duplication → Detect duplicate code blocks.
- Technical Debt → Estimate the effort required to fix all the issues in the code.

2\. Multi-Language Support

Supports a wide range of programming languages, including:

- Java
- JavaScript
- C#
- C++
- Python
- PHP

3\. Quality Gates

Set thresholds for quality metrics to ensure code meets the standards before it is integrated into the main branch.

### Similar Tools

- SonarQube
- Checkmarx
- Coverity
- Fortify Static Code Analyzer (SCA)
- Veracode
- PMD
- ESLint
- FindBugs / SpotBugs
- StyleCop
- Pylint
- ReSharper
- Flake8
- Codacy
- CodeClimate
- DeepSource
- Semmle LGTM
- Klocwork
- Infer
- Bandit
- Rubocop
- Brakeman
- Snyk

## 2. Difference Between Code Review and Code Coverage

### Code Coverage

Number of lines tested through unit test cases is called code coverage.

- In a project, 80% coverage is a commonly used target.
- Benefits: Helps identify untested areas of code.
- Tools: JaCoCo (for Java).

### Code Review

Code review is the process of manually reviewing code by team members to:

- Find bugs.
- Ensure coding standards are followed.
- Improve overall code quality.

Benefits:

- Better software quality.
- Fewer bugs.
- Promotes best practices within the development team.

Tools: GitHub Pull Requests.

## 3. SonarQube Introduction

| Property                  | Details                                                              |
| ------------------------- | -------------------------------------------------------------------- |
| Type                      | Continuous Code Quality                                              |
| Vendor                    | Sonar                                                                |
| Open Source               | Some editions and features are available under open-source licensing |
| Version in original notes | 9.x                                                                  |
| Supporting OS             | Cross-platform                                                       |
| Executable software       | No; download as a ZIP and extract it                                 |

Download link:

[https://www.sonarsource.com/products/sonarqube/downloads/](https://www.sonarsource.com/products/sonarqube/downloads/?utm_source=chatgpt.com)

### More About SonarQube

- SonarQube was previously called Sonar.
- It is an open-source software quality management tool.
- It continuously analyses and measures the quality of source code.
- If any issues are found, it displays reports in the dashboard.
- It supports multiple languages like Java, C#, JavaScript, etc. Initially, it was developed for Java.
- It supports operating systems like macOS, Linux, Windows, etc.
- It supports databases like Oracle and PostgreSQL, depending on the edition and configuration.
- It supports browsers like Chrome, Safari, Internet Explorer, Microsoft Edge, etc.

### Issues Identified by SonarQube

1. Duplicate code.
2. Coding standards.
3. Unit test coverage.
4. Complex code.
5. Comments.
6. Potential bugs (exception handling).
7. Other code-quality and security issues.

Note: With the help of SonarQube reports, we can stop deployment if the code does not meet the required quality standards.

## 4. Prerequisites for SonarQube Installation

To install SonarQube, certain prerequisites must be met in terms of software and hardware requirements.

### 4.1 Hardware Requirements

| Component  | Requirement                                                        |
| ---------- | ------------------------------------------------------------------ |
| CPU        | Modern multi-core processor                                        |
| RAM        | At least 2 GB; 4 GB recommended for the lab setup                  |
| Disk Space | 1 GB free for installation, plus additional space for the database |

EC2: t2.medium (as in the original lab notes).

### 4.2 Software Requirements

Operating System

- Linux (preferred)
- Windows
- macOS

Java

- Java JDK version depends on the SonarQube release.
- SonarQube requires a compatible JDK, not just a JRE.
- Set the `JAVA_HOME` environment variable to point to your JDK installation.

Database

- Oracle
- MS SQL Server
- PostgreSQL
- MySQL support was removed from SonarQube.

Note: H2 is an inbuilt database used for evaluation and testing. For production, use a supported external database.

## 5. SonarQube Installation on RHEL 10 (EC2)

Original lab version: SonarQube 9.6.1.

The following commands are from the installation lab. Java and SonarQube versions must be compatible; do not assume these old-version instructions apply unchanged to newer releases.

### Prerequisites

- EC2 Instance: t2.medium.
- OS: Red Hat Enterprise Linux 10.
- Open Ports:
  - 22 for SSH.
  - 9000 for SonarQube web interface.
- Internet access to download packages.

### Step 1: Connect to EC2

```
ssh -i your-key.pem ec2-user@<your-ec2-public-ip>
```

### Step 2: Switch to Root

```
sudo su -
```

### Step 3: Fix Time Synchronization

```
timedatectl set-ntp true

timedatectl set-timezone UTC

systemctl restart chronyd || systemctl restart systemd-timesyncd

timedatectl
```

Check whether NTP is synchronized.

### Step 4: Install Java 21 (Amazon Corretto)

```
rpm --import https://yum.corretto.aws/corretto.key

curl -Lo /etc/yum.repos.d/corretto.repo https://yum.corretto.aws/corretto.repo

yum install java-21-amazon-corretto-devel -y
```

### Step 5: Set Java 21 as Default

```
alternatives --config java
```

Select the option for Java 21.

Example:

```
/usr/lib/jvm/java-21-amazon-corretto/bin/java
```

### Step 6: Set Required Kernel Parameters

```
sysctl -w vm.max_map_count=262144

echo 'vm.max_map_count=262144' >> /etc/sysctl.conf
```

### Step 7: Install Required Packages

```
yum install wget unzip -y
```

### Step 8: Download and Extract SonarQube

```
cd /opt
```

Download the required SonarQube ZIP from the official distribution page.

Example from the original lab:

```
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-26.10.0.133078.zip
```

Extract:

```
unzip sonarqube-26.10.0.133078.zip
```

Rename:

```
mv sonarqube-26.10.0.133078 sonarqube
```

### Step 9: Create a Dedicated User

You should not start SonarQube with the root user.

You should start SonarQube with the `sonar` user.

```
useradd sonar
```

Set ownership:

```
chown -R sonar:sonar /opt/sonarqube
```

Set permissions:

```
chmod -R 775 /opt/sonarqube
```

### Step 10: Configure Environment for Sonar User

Switch to the sonar user:

```
su - sonar
```

Add the following to `~/.bashrc`:

```
echo 'export JAVA_HOME=/usr/lib/jvm/java-21-amazon-corretto' >> ~/.bashrc

echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc
```

Reload:

```
source ~/.bashrc
```

Verify:

```
java -version
```

It should return Java 21 for the example setup, provided that the selected SonarQube version supports it.

### Step 11: Start SonarQube

```
cd /opt/sonarqube/bin/linux-x86-64/
```

Start:

```
sh sonar.sh start
```

Check status:

```
sh sonar.sh status
```

Stop:

```
sh sonar.sh stop
```

### Step 12: Access SonarQube

Open in your browser:

```
http://<your-ec2-public-ip>:9000
```

Default credentials:

```
Username: admin
Password: admin
```

For older versions, the lab notes mention `admin123`.

SonarQube will immediately ask you to update the password. Change it.

### Troubleshooting

If SonarQube does not start, check the logs:

```
tail -n 100 /opt/sonarqube/logs/sonar.log

tail -n 100 /opt/sonarqube/logs/es.log
```

Important Notes:

- SonarQube should not be run as root.
- Java compatibility depends on the SonarQube version.
- SonarQube may fail to start if memory is insufficient.
- Configure `vm.max_map_count` correctly.

## 6. Real-Time Issue: SonarQube Not Starting

Most people face this issue when they accidentally start the SonarQube server with the root user.

### Step 1: Switch to Root

```
sudo su -
```

Navigate to the SonarQube directory:

```
cd /opt/sonarqube/bin/linux-x86-64
```

Start SonarQube:

```
sh sonar.sh start
```

It may show that the server has started, but actually it may not be running.

Check:

```
sh sonar.sh status
```

### Step 2: Try with Sonar User

```
su - sonar
```

Navigate:

```
cd /opt/sonarqube/bin/linux-x86-64
```

Start:

```
sh sonar.sh start
```

Check:

```
sh sonar.sh status
```

If you get an error, check the logs.

### Step 3: Check Logs

```
cd /opt/sonarqube/logs
```

List files:

```
ls -lrth
```

Check the SonarQube log:

```
cat sonar.log
```

The original lab notes mention that a temporary directory was created at:

```
/opt/sonarqube/temp/
```

Navigate:

```
cd /opt/sonarqube
ls
```

Expected directories include:

```
logs
temp
```

Navigate:

```
cd temp/conf/
cd es/
ls
```

The lab notes list these files:

```
elasticsearch.keystore
elasticsearch.yml
jvm.options
log4j2.properties
```

### Step 4: Delete Temporary Directory (Only If Required)

The original lab troubleshooting step was:

```
sudo rm -rf /opt/sonarqube/temp/
```

Then start SonarQube again using the `sonar` user:

```
cd /opt/sonarqube/bin/linux-x86-64

sh sonar.sh start

sh sonar.sh status
```

Important: Do not delete the temporary directory as a routine fix. First inspect the logs and identify the actual problem.

Access:

```
http://<your-ec2-public-ip>:9000/
```

## 7. Generate SonarQube Reports Using Maven

Actually, to generate a SonarQube report, we don't necessarily need to create a package. We can run the Maven build and analysis.

Maven command:

```
mvn clean package
```

Maven lifecycle:

```
validate
   |
compile
   |
test
   |
package
```

## 8. Connect Maven EC2 with SonarQube EC2

We need two EC2 instances:

1. Maven EC2.
2. SonarQube EC2.

### Step 1: Connect to Maven EC2

```
sudo su -
```

### Step 2: Connect to SonarQube EC2

```
su - sonar
```

### Step 3: Clone the Maven Project

On Maven EC2:

```
git clone https://github.com/kkdevopsb9/maven-webapplication-project-kkfunda.git
```

Navigate:

```
cd maven-webapplication-project-kkfunda/
```

### Step 4: Build the Project

```
mvn clean package
```

Check the files:

```
ls
```

Expected output:

```
Jenkinsfile
README.md
k8s
kubernetes
pom.xml
src
target
```

### Step 5: Check pom.xml

```
cat pom.xml
```

Example properties section:

```
<properties>
    <jdk.version>17</jdk.version>
    <spring.version>5.3.34</spring.version>
    <junit.version>4.11</junit.version>
    <log4j.version>1.2.17</log4j.version>

    <sonar.host.url>http://<sonarqube-public-ip>:9000/</sonar.host.url>

    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
</properties>
```

We need to configure the SonarQube server URL.

### Step 6: Update pom.xml

```
vi pom.xml
```

Update the SonarQube URL:

```
<sonar.host.url>http://<sonarqube-public-ip>:9000/</sonar.host.url>
```

Note: Do not hardcode your admin username and password in `pom.xml`.

### Step 7: Open Port 9000

In the SonarQube EC2 Security Group:

- Open port 9000.
- Allow your own IP for browser access.
- Allow the Maven EC2 public IP for Maven-to-SonarQube communication.

For production, prefer private networking and restrict access to the required source security group rather than exposing the SonarQube port publicly.

## 9. Execute SonarQube Analysis Using Maven

After running:

```
mvn clean package
```

The code is already compiled and packaged.

We can execute SonarQube analysis.

### Command 1

```
mvn sonar:sonar
```

However, the original lab encountered this error:

```
[ERROR] No plugin found for prefix 'sonar'
in the current project and in the plugin groups
```

### Difference Between the Commands

Command 1:

```
mvn clean package sonar:sonar
```

Syntax:

```
pluginName:goalName
```

Explanation:

- `sonar:sonar` uses Maven plugin prefix resolution.
- Maven must identify which plugin belongs to the `sonar` prefix.
- In the lab, Maven could not resolve that prefix.

Command 2:

```
mvn clean package org.sonarsource.scanner.maven:sonar-maven-plugin:5.8.0.7211:sonar
```

Explanation:

- Specifies the plugin's exact group ID.
- Specifies the artifact ID.
- Specifies the plugin version.
- Specifies the goal.
- Maven knows exactly which plugin to download and execute.

### How to Make `mvn sonar:sonar` Work

Add the SonarQube Maven plugin inside the `pom.xml` file under the `<build><plugins>` section.

```
<build>
    <plugins>
        <plugin>
            <groupId>org.sonarsource.scanner.maven</groupId>
            <artifactId>sonar-maven-plugin</artifactId>
            <version>5.8.0.7211</version>
        </plugin>
    </plugins>
</build>
```

Then run:

```
mvn clean package sonar:sonar
```

For your notes:

| Command                           | Meaning                                                      |
| --------------------------------- | ------------------------------------------------------------ |
| `sonar:sonar`                     | Short plugin prefix; requires prefix resolution              |
| `groupId:artifactId:version:goal` | Exact plugin coordinates; avoids relying on prefix discovery |

### Working Command from the Original Lab

```
mvn clean package org.sonarsource.scanner.maven:sonar-maven-plugin:5.8.0.7211:sonar -Dsonar.host.url=http://<sonarqube-public-ip>:9000 -Dsonar.token=<SONAR_TOKEN>
```

## 10. Connect SonarQube Using an Authentication Token

### Requirement

Instead of using a username and password hardcoded in `pom.xml`, we can use the SonarQube server token.

### Step 1: Generate a Token

1. Login to SonarQube.
2. Go to Administration.
3. Open Security.
4. Go to Users.
5. Click Tokens.
6. Give the token a name.
7. Click Generate.
8. Copy the generated token.

Important: Copy and store the token securely. Do not share it or commit it to GitHub.

### Step 2: Update Authentication in Maven

There are different ways to provide the token.

Option A: Using Maven command line

```
mvn clean package org.sonarsource.scanner.maven:sonar-maven-plugin:5.8.0.7211:sonar \
-Dsonar.host.url=http://<sonarqube-public-ip>:9000 \
-Dsonar.token=<SONAR_TOKEN>
```

Option B: Using pom.xml properties

```
<properties>
    <sonar.host.url>http://<sonarqube-public-ip>:9000</sonar.host.url>
</properties>
```

Then pass the token through the command line:

```
mvn clean package sonar:sonar -Dsonar.token=<SONAR_TOKEN>
```

Note: Avoid storing authentication tokens directly in `pom.xml`. For Jenkins, use Jenkins Credentials.

### Step 3: Generate SonarQube Report

If the project has already been compiled:

```
mvn sonar:sonar -Dsonar.token=<SONAR_TOKEN>
```

If the project needs to be compiled and packaged first:

```
mvn clean package sonar:sonar -Dsonar.token=<SONAR_TOKEN>
```

## 11. Verify SonarQube Dashboard

### Step 1: Open SonarQube Dashboard

1. Login to SonarQube.
2. Go to Projects.
3. Open your project: `maven-web-application`.
4. Verify the following sections:

- Overview
- Issues
- Security Hotspots
- Measures
- Code
- Activity

### Step 2: Overview

Check the overall code quality.

Example results from the original lab:

| Metric            | Result |
| ----------------- | ------ |
| Bugs              | 6      |
| Vulnerabilities   | 0      |
| Security Hotspots | 2      |
| Technical Debt    | 2 min  |
| Code Smells       | 1      |
| Coverage          | 0.0%   |
| Duplications      | 0.0%   |

### Step 3: Security Hotspots

Example identified issue:

- Credentials hardcoded in the source code.
- Review the hotspot and fix the issue if necessary.

### Step 4: Measures

Check the estimated effort required to fix code-quality issues, including technical debt.

### Step 5: Code

Example:

- `src/main` — 158 lines of code.
- Review bugs, vulnerabilities, code smells, security hotspots, coverage, and duplications.
- `pom.xml` — Review dependency and project configuration information.

### Step 6: Issues

- Bugs → Review potential code errors.
- Code Smells → Review code that needs improvement.

### Step 7: Activity

- View the project's analysis history and snapshots.
- Compare code-quality results across different analyses.

### Step 8: Code Metrics

- Duplications.
- Coverage.
- Lines of code.
- Files analyzed.

Purpose: Use the SonarQube dashboard to identify code-quality issues, security risks, duplicated code, and test-coverage gaps.

## 12. How Does SonarQube Identify Java/Python?

SonarQube identifies the programming language mainly by checking the file extension of each source file.

### Example

| File       | Language identified |
| ---------- | ------------------- |
| Main.java  | Java                |
| App.java   | Java                |
| app.py     | Python              |
| script.js  | JavaScript          |
| index.html | HTML                |
| pom.xml    | XML                 |

### How It Works

1. You run the Maven SonarQube analysis.
2. The scanner scans the project files.
3. It identifies languages based on file extensions and available language analyzers.
4. It applies the appropriate language-specific rules.
5. It sends the analysis results to the SonarQube server.
6. The dashboard displays bugs, vulnerabilities, code smells, and other metrics.

### Real-Time Example

Project structure:

```
maven-webapplication-project-kkfunda/
│
├── pom.xml
│
└── src/
    └── main/
        ├── java/
        │   └── com/kk/Main.java
        │
        └── webapp/
            └── index.jsp
```

- `Main.java` → Java analyzer.
- `index.jsp` → Analyzed according to supported language analyzers and rules for its contents.
- `pom.xml` → XML file, not Java source code.

Important: SonarQube does not identify a language simply from the folder name. File extensions and installed/supported analyzers determine which language rules can be applied.

### Where to Check File Extensions in SonarQube

Navigate:

```
SonarQube Dashboard
        ↓
Administration
        ↓
Languages
        ↓
Select Java
        ↓
File Extensions / Suffixes
```

## 13. How SonarQube Finds Bugs

1. SonarQube Dashboard → Rules.
2. Rules contain predefined checks for supported programming languages, such as Java and Python.
3. During code analysis, SonarQube applies the relevant rules to the source code.
4. When code violates a rule, SonarQube reports the issue as a bug, vulnerability, or code smell, depending on the rule.

### Example

Java code uses a variable that may cause a `NullPointerException`.

SonarQube can detect this using its static code analysis rules.

In short:

```
Dashboard
    ↓
Rules
    ↓
Language-specific rules
    ↓
Analyze source code
    ↓
Report issues
```

## 14. Quality Profile

### What Is a Quality Profile?

A Quality Profile contains a set of rules used by SonarQube to analyze source code.

It determines which rules are active for a particular programming language.

### Step 1: Create a Quality Profile

1. Login to SonarQube.
2. Go to Quality Profiles.
3. Select the language, such as Java.
4. Click Create.
5. Enter a profile name: `customqp`.
6. Create the profile.

### Step 2: Activate Rules

1. Open your newly created Quality Profile.
2. Click Activate More.
3. Select the rules requested by developers.
4. Click Activate.

Now try creating a package on the Maven server.

```
mvn clean package
```

Important:

Creating a Quality Profile does not mean the project automatically uses that profile.

Check:

```
SonarQube
    ↓
Quality Profiles
    ↓
Search Java
```

You may find that `customqp` is not being used. Instead, the default profile `Sonar way` is being used.

### Step 3: Map customqp Quality Profile to Your Project

1. Login to the SonarQube dashboard.
2. Go to Projects.
3. Open your project: `maven-web-application`.
4. Go to Policies → Quality Profiles.
5. Find the Java language section.
6. Click Change / Select Quality Profile.
7. Select `customqp`.

Your project is now mapped to the custom Java Quality Profile.

### Step 4: Verify from Maven Server

Run your Maven analysis command:

```
mvn clean package sonar:sonar
```

Then open the project in SonarQube and check Quality Profiles and Issues to verify that the analysis uses the rules from `customqp`.

### Step 5: Verify the Results

1. Open your project in SonarQube.
2. Go to Quality Profiles or the project's analysis details.
3. Confirm that your custom profile is assigned to Java.
4. Check Issues to see the results produced by the activated rules.

Important Difference:

- Quality Profiles → Control which code-analysis rules are active.
- Quality Gates → Determine whether the analysis passes or fails.

## 15. Set customqp as the Default Quality Profile

We can also set our own created Quality Profile as the default, but it is not always the best practice.

### Steps

1. Login to the SonarQube dashboard.
2. Go to Quality Profiles.
3. Select the Java language.
4. Find your custom profile: `customqp`.
5. Click the three-dot menu (⋮) next to `customqp`.
6. Select Set as Default and confirm if prompted.

### Verify

`customqp` should display a Default label.

Note: This sets `customqp` as the default for Java projects that don't have another profile explicitly assigned. Existing project-specific assignments may remain unchanged.

## 16. Quality Gate

### 16.1 What Is a Quality Gate?

A Quality Gate is a set of conditions used to decide whether the code meets the required quality standards.

- If all conditions pass → Quality Gate Passed.
- If any condition fails → Quality Gate Failed.

Example:

If code coverage is below 80%, the Quality Gate fails when that condition is configured.

### 16.2 What Is the Default Quality Gate?

The Default Quality Gate is the Quality Gate automatically assigned to projects that do not have another Quality Gate explicitly assigned.

SonarQube provides a built-in Quality Gate commonly named `Sonar way`.

### 16.3 Create a Quality Gate Named customqg

Step 1: Create the Quality Gate

1. Login to SonarQube.
2. Go to Quality Gates.
3. Click Create.
4. Enter the name: `customqg`.
5. Click Create.

Step 2: Add Conditions

Add these three conditions to `customqg`.

| Condition            | Operator        | Value |
| -------------------- | --------------- | ----- |
| Coverage             | Is less than    | 80%   |
| Line Coverage        | Is less than    | 80%   |
| Duplicated Lines (%) | Is greater than | 4%    |

For each condition:

1. Click Add Condition.
2. Select the metric.
3. Select the operator.
4. Enter the threshold value.
5. Save the condition.

Step 3: Set customqg as the Default Quality Gate

1. Go to Quality Gates.
2. Find `customqg`.
3. Open its options menu.
4. Select Set as Default.

Now `customqg` will be assigned by default to projects without an explicit Quality Gate assignment.

Step 4: Map customqg to Your Project

1. Open Projects.
2. Select `maven-web-application`.
3. Open Policies → Quality Gate.
4. Select `customqg`, if it is not already assigned.

Step 5: Verify Using Maven

Run your existing Maven analysis command:

```
mvn clean package sonar:sonar
```

Open your project in SonarQube and check:

```
Overview → Quality Gate
```

Expected Result:

The Quality Gate fails if:

- Coverage is below 80%.
- Line coverage is below 80%.
- Duplicated lines percentage is above 4%.

Note: These are overall-code metrics unless you specifically configure the conditions for new code. Coverage metrics may also depend on whether test coverage reports are imported into SonarQube.

## 17. SonarQube Administration – User Management

### Step 1: Create User kiran in SonarQube

1. Login to SonarQube as the administrator.
2. Go to Administration → Security → Users.
3. Click Create User.
4. Enter the details:

```
Login: kiran
Name: Kiran
Email: Enter a valid email address
Password: Set a password
```

5\. Click Create.

### Step 2: Log Out from Administrator Account

1. Click your profile icon in the top-right corner.
2. Click Log out.

### Step 3: Login as kiran

Open your SonarQube URL:

```
http://<sonarqube-public-ip>:9000
```

Enter:

```
Username: kiran
Password: <password you configured>
```

Click Log in.

Expected Result:

You should now be logged in to SonarQube as kiran.

But kiran cannot edit. The Administration tab is also not shown for kiran.

Reason: The user does not have the required permissions.

If SonarQube prompts you to change the password on first login, set a new password and continue.

## 18. Give Full Access to a User

### Step 1: Create User anand

1. Login to SonarQube as an administrator.
2. Go to Administration → Security → Users.
3. Click Create User.
4. Enter the details:

```
Login: anand
Name: Anand
Email: Enter a valid email address
Password: Set a password
```

5\. Click Create.

### Step 2: Give Anand Full Access

1. Go to Administration → Security → Global Permissions.
2. Find the Administer System permission.
3. Click Edit or the corresponding permission management option.
4. Grant anand the Administer System permission.

For full administrative access, Anand may also need:

- Administer Quality Gates.
- Administer Quality Profiles.

For access to all projects, grant the appropriate project permissions or add Anand to a group that has them.

### Step 3: Verify

1. Log out of the administrator account.
2. Login as anand.
3. Verify that Anand can access the required administration and project-management features.

Note: Give full administrator permissions only to trusted users who need them for their DevOps responsibilities.

## 19. Create a Group and Assign Permissions

### Step 1: Create the Group DevOps Group

1. Login to SonarQube as an administrator.
2. Go to Administration → Security → Groups.
3. Click Create Group.
4. Enter:

```
Name: DevOps Group

Description: DevOps team with administrative and analysis permissions
```

5\. Click Create.

### Step 2: Add anand to the Group

1. Go to Administration → Security → Users.
2. Search for anand.
3. Click the Groups option for Anand.
4. Add DevOps Group.
5. Save the changes.

### Step 3: Assign Permissions to DevOps Group

Go to:

```
Administration → Security → Global Permissions
```

Find DevOps Group and grant these permissions:

| Permission                  | Purpose                                   |
| --------------------------- | ----------------------------------------- |
| Administer System           | Administer SonarQube system settings      |
| Administer Quality Gates    | Create and manage Quality Gates           |
| Administer Quality Profiles | Create and manage Quality Profiles        |
| Execute Analysis            | Run SonarQube analysis from Maven/Jenkins |
| Create Projects             | Create new SonarQube projects             |

### Step 4: Verify

1. Log out of the administrator account.
2. Login as anand.
3. Verify that Anand can access the required administration features and create projects or execute analysis.

Expected Result:

Anand receives these permissions through membership in DevOps Group.

Other members added to this group will also inherit its granted permissions.

The people/users under this group will get the assigned accesses.

Like this, we can give limited access to users and groups.

## 20. SonarQube in Production and Lower Environments

In the original mentor notes:

- In production environments, SonarQube does not exist.
- In lower environments, SonarQube is mandatory because code is scanned already at lower environments.

Team separation:

```
Development-related DevOps Team

Production-related DevOps Team
```

### Real-Time Workflow

Actually, the final result report is sent to developers from Jenkins through Slack.

Then developers login to SonarQube and check the report.

```
Developer
    |
    v
GitHub
    |
    v
Jenkins
    |
    v
Maven Build
    |
    v
SonarQube Analysis
    |
    v
Quality Gate
    |
    v
Report
    |
    v
Slack Notification
    |
    v
Developer Reviews SonarQube Dashboard
```

# Quick Revision – SonarQube

| Topic                   | Important Point                             |
| ----------------------- | ------------------------------------------- |
| SonarQube               | Continuous code quality inspection          |
| Static Code Analysis    | Finds bugs, vulnerabilities and code smells |
| Code Coverage           | Measures tested code                        |
| Code Review             | Manual review by developers                 |
| Quality Profile         | Collection of active analysis rules         |
| Quality Gate            | Conditions to pass or fail code quality     |
| Sonar Token             | Authentication for Maven/Jenkins analysis   |
| SonarQube Port          | 9000                                        |
| SonarQube User          | Dedicated `sonar` user                      |
| SonarQube Logs          | `/opt/sonarqube/logs/`                      |
| SonarQube Installation  | ZIP download and extraction                 |
| Maven Scanner           | `sonar-maven-plugin`                        |
| Default Quality Profile | Sonar way                                   |
| Default Quality Gate    | Sonar way                                   |

Important commands for revision:

```
# Check Java
java -version

# Check kernel parameter
sysctl vm.max_map_count

# Install packages
yum install wget unzip -y

# Switch to sonar user
su - sonar

# Start SonarQube
sh sonar.sh start

# Check SonarQube status
sh sonar.sh status

# Stop SonarQube
sh sonar.sh stop

# Check SonarQube logs
tail -n 100 /opt/sonarqube/logs/sonar.log

# Build Maven project
mvn clean package

# Run SonarQube analysis
mvn clean package sonar:sonar

# Run analysis using token
mvn clean package sonar:sonar -Dsonar.token=<SONAR_TOKEN>
```
