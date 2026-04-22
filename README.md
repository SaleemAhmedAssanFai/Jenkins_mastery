# Jenkins Mastery 🚀
**CI/CD Automation · Pipeline Engineering · Build Management · Configuration**

A hands-on learning repository documenting my journey to master Jenkins —
covering core concepts, pipeline automation, and CI/CD best practices through
practical labs and real configurations.

---

> **Repository Note**
>
> The labs in this repository are hands-on exercises designed to simulate
> real-world Jenkins workflows. Each section focuses on a specific Jenkins
> concept — from build triggers and environment variables to declarative
> pipelines and artifact deployment. The goal is to demonstrate how an
> engineer configures, automates, and manages CI/CD pipelines using
> Jenkins and industry-standard tools.

---

## 📁 Repository Structure
```
Jenkins_mastery/
├── build triggers/
│   ├── Build trigger.png
│   ├── trigger builds remotely.png
│   └── README.md
├── variables in jenkins/         🔄 Coming Soon
├── build environments/           🔄 Coming Soon
├── build pipeline/               🔄 Coming Soon
├── deploy artifacts to tomcat/   🔄 Coming Soon
├── deploy build to tomcat/       🔄 Coming Soon
└── declarative pipeline/         🔄 Coming Soon
```

---

## 📚 Topics Covered

| # | Topic | Status |
|---|---|---|
| 1 | [Build Triggers](<./build triggers/README.md>) | ✅ Complete |
| 2 | Variables in Jenkins | 🔄 Coming Soon |
| 3 | Build Environments | 🔄 Coming Soon |
| 4 | Build Pipeline | 🔄 Coming Soon |
| 5 | Deploy Artifacts to Tomcat Server | 🔄 Coming Soon |
| 6 | Deploy Build to Tomcat Server using Jenkins | 🔄 Coming Soon |
| 7 | Declarative Pipeline | 🔄 Coming Soon |

---

## 🔔 Build Triggers

Build Triggers define **when** Jenkins should automatically start a build.
Instead of clicking "Build Now" manually every time, you configure Jenkins
to react to events, schedules, or external signals automatically.

To access Build Triggers in any Jenkins job:
> **Job → Configure → Build Triggers**

The screenshot below shows all the build trigger options available inside
a Jenkins Freestyle job configuration:

![Build Triggers Overview](build%20triggers/Build%20trigger.png)

Jenkins provides the following trigger options:

- **Trigger builds remotely** — fire a build via an HTTP URL and token
- **Build after other projects are built** — chain jobs together sequentially
- **Build periodically** — schedule builds using cron syntax
- **GitHub hook trigger for GITScm polling** — trigger on GitHub push events
- **Poll SCM** — periodically check the repository for new changes

---

### Phase 1 — Job Setup

**Step 1: Create the Jenkins Job**

Created a new Freestyle job called `fourth_job`:

> Jenkins Dashboard → New Item → Enter name: `fourth_job` → Freestyle Project → OK

**Step 2: Configure Source Code Management**

Under the **Source Code Management** section, selected **Git** and provided
the repository URL:
```
https://github.com/SaleemAhmedAssanFai/Jenkins_mastery.git
```

This tells Jenkins where to pull the code from when the build is triggered.

---

### Phase 2 — Configuring Trigger Builds Remotely

**Step 3: Enable the Trigger**

Inside `fourth_job → Configure → Build Triggers`, checked the option:

> ✅ Trigger builds remotely (e.g., from scripts)

**Step 4: Generate an Authentication Token**

In the **Authentication Token** field, entered a secret token:
```
mytoken
```

This token acts as a password — only requests that include this token in
the URL will be able to trigger the build remotely.

> 📸 *Screenshot: Trigger builds remotely option enabled with token field*

![Trigger Builds Remotely](build%20triggers/trigger%20builds%20remotely.png)

Clicked **Save** to apply the configuration.

---

### Phase 3 — Triggering the Build Remotely

**Step 5: Construct the Trigger URL**

Jenkins exposes a special URL for remote triggering. The format is:
```
http://JENKINS_URL/job/JOB_NAME/build?token=YOUR_TOKEN
```

For this lab, the URL used was:
```
http://192.168.11.150:8080/job/fourth_job/build?token=mytoken
```

**Step 6: Fire the Build**

Pasted the URL directly into a browser and hit Enter. Jenkins received
the request, authenticated it using `mytoken`, and immediately queued
a new build.

**Result:** Build **#5** of `fourth_job` was triggered successfully —
without clicking "Build Now" inside Jenkins.

---

## 🔑 Key Lessons Learned

**1. The Token is Your Only Security Layer**

When using Trigger Builds Remotely, the authentication token is the only
thing protecting your job from unauthorized triggers. Treat it like a
password — never expose it in public repositories or logs.

**2. Source Code Management Must Be Configured First**

Jenkins needs to know where to pull code from before a remote trigger
makes sense. Always configure the Git repository under Source Code
Management before setting up any build trigger.

**3. The IP Address is Environment-Specific**

The trigger URL uses your Jenkins server's IP address. In this lab,
Jenkins is running locally at `192.168.11.150:8080`. In a production
environment this would be a real domain or an EC2 public IP. The URL
format stays the same — only the host changes.

**4. Remote Triggers Don't Require Jenkins UI Access**

Once configured, anyone or any system with the token and URL can fire
the build — from a browser, a curl command, a script, or a third-party
tool. This makes it a powerful integration point for automation.

---

## 🔧 Variables in Jenkins
 
Variables in Jenkins allow jobs to use dynamic values instead of
hardcoding information directly into build scripts. Jenkins supports two
types of variables, each with a different scope:
 
| Type | Scope | Defined In |
|---|---|---|
| **Environment Variables** | The job they are configured in only | Job → Configure → Environment |
| **Global Variables** | All jobs across the entire Jenkins instance | Manage Jenkins → System → Global Properties |
 
---
 
### Part 1 — Environment Variables
 
Environment variables in Jenkins are scoped to the **job they are defined
in**. They are not accessible by any other job. Jenkins also provides a
set of built-in environment variables that are automatically available in
every job — values like `BUILD_ID` and `JOB_NAME` that Jenkins injects
at build time without any extra configuration.
 
**Step 1: Create a New Job**
 
Created a new Freestyle job to demonstrate environment variables:
 
> Jenkins Dashboard → New Item → Enter job name → Freestyle Project → OK
 
**Step 2: Navigate to the Environment Section**
 
Inside the job configuration, clicked **Environment** in the left sidebar:
 
> Job → Configure → Environment
 
**Step 3: Add an Execute Shell Build Step**
 
Scrolled down to **Build Steps** and clicked:
 
> Add build step → Execute shell
 
**Step 4: View Available Environment Variables**
 
Inside the **Command** field, Jenkins displays a link:
 
> 📎 *See the list of available environment variables*
 
Clicking that link opens a full reference of all built-in variables Jenkins
provides for every build — things like `BUILD_ID`, `JOB_NAME`, `WORKSPACE`,
`BUILD_URL`, and more. These can be used directly in any shell command
without defining them first.
 
**Step 5: Write a Shell Script Using Environment Variables**
 
In the **Command** field, wrote a script that mixes locally declared
variables with Jenkins built-in environment variables:
 
```bash
B=40
echo "The Value of A is ${A}"
echo "The Value of B is ${B}"
Name=Saleem
echo "My name is ${Name} Ahmed."
echo "My build id is ${BUILD_ID}"
echo "The job name is ${JOB_NAME}"
```
 
> 📸 *Screenshot: Execute shell command field showing environment variables
> in use — local declarations alongside built-in Jenkins variables*
 
![Environment Variables](variables%20in%20jenkins/environment_variables.png)
 
Breaking down each line:
 
| Line | Description |
|---|---|
| `B=40` | Locally declared variable — scoped to this shell step |
| `echo "The Value of A is ${A}"` | References `A` — a value expected from outside the script |
| `echo "The Value of B is ${B}"` | Prints `40` — the locally declared value above |
| `Name=Saleem` | Locally declared variable |
| `echo "My name is ${Name} Ahmed."` | Prints `My name is Saleem Ahmed.` |
| `echo "My build id is ${BUILD_ID}"` | Built-in Jenkins variable — auto-injected each build |
| `echo "The job name is ${JOB_NAME}"` | Built-in Jenkins variable — resolves to the job's name |
 
Clicked **Save** to apply.
 
**Result:** When the build ran, Jenkins resolved every variable and printed
the values in the Console Output. `${BUILD_ID}` and `${JOB_NAME}` were
populated automatically by Jenkins — no manual setup required.
 
---
 
### Part 2 — Global Variables
 
Global variables are defined at the **Jenkins system level** and are
available to **every job and pipeline** on the instance. They are ideal
for values that multiple jobs need to share — such as a server URL, a
tool path, or a deployment target — defined once and reused everywhere.
 
**Step 1: Open Manage Jenkins**
 
From the Jenkins Dashboard, clicked:
 
> Jenkins Dashboard → Manage Jenkins
 
**Step 2: Select System**
 
Under the **System Configuration** section, clicked:
 
> System
 
**Step 3: Scroll Down to Global Properties**
 
Scrolled down the System configuration page until reaching the
**Global properties** section. Checked the box:
 
> ✅ Environment variables
 
This expanded the section to reveal **Name** and **Value** input fields.
 
**Step 4: Declare the Global Variable**
 
Clicked **Add** and entered a name and value for the new global variable:
 
| Field | Value |
|---|---|
| Name | *(your variable name)* |
| Value | *(your variable value)* |
 
> 📸 *Screenshot: Global properties — Environment variables section with
> a custom global variable declared*
 
![Global Variables](variables%20in%20jenkins/global_variables.png)
 
**Step 5: Save and Apply**
 
Clicked **Save**. The global variable is now registered across the entire
Jenkins instance.
 
**Result:** Any job — existing or new — can now reference this variable
in its build steps using `${VARIABLE_NAME}`, exactly like a built-in
environment variable, without needing to declare it inside the job itself.
 
---
 
## 🔑 Key Lessons Learned (Variables)
 
**1. Environment Variables Are Job-Scoped**
 
Variables defined inside a job's Environment or Execute shell step exist
only for that job's build. They cannot be read by any other job and are
reset on every new build run.
 
**2. Global Variables Are Instance-Wide**
 
A global variable defined in Manage Jenkins → System → Global Properties
is available to every single job on the Jenkins server. Change the value
once and every job that references it picks up the new value on its next
build — no job-level changes needed.
 
**3. Jenkins Built-in Variables Require No Setup**
 
`${BUILD_ID}`, `${JOB_NAME}`, `${WORKSPACE}`, and all other built-in
variables are injected by Jenkins automatically into every build. They
can be referenced directly in any shell command or pipeline script.
 
**4. The Variable List Link Is Your Reference**
 
The *"See the list of available environment variables"* link inside the
Execute shell command field opens a live, version-specific reference of
every built-in variable Jenkins provides. Always check it before writing
a new script.
 
**5. Undefined Variables Print Empty — Not an Error**
 
If a variable is referenced but not declared or passed in, Jenkins
resolves it to an empty string rather than throwing an error. Always
verify that every variable a script depends on has been defined at the
appropriate scope before the build runs.
 
---
 
## 🛠️ Tools & Environment
 
| Tool | Purpose |
|---|---|
| Jenkins | CI/CD automation server (localhost:8080) |
| GitHub | Source code hosting |
| Tomcat | Application server for deployments |
| Groovy | Declarative pipeline scripting |
| Git | Version control |
 
---
 
## 📌 Project Status
 
✅ Build Triggers — Trigger Builds Remotely documented  
✅ Variables in Jenkins — Environment Variables and Global Variables documented  
⬜ Build Environments  
⬜ Build Pipeline  
⬜ Deploy Artifacts to Tomcat Server  
⬜ Deploy Build to Tomcat Server using Jenkins  
⬜ Declarative Pipeline  
 
---
 
## 🔗 Connect
 
[LinkedIn](https://www.linkedin.com/in/saleemfai) · 📍 Cameroon
 
