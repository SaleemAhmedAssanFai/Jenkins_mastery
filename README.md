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
├── variables in jenkins/
│   ├── environment_variables.png
│   ├── global_variables.png
│   └── README.md
├── build_environments/
│   ├── build_timeout.png
│   ├── choice_and_multiline_parameters.png
│   ├── parametized_job.png
│   ├── parametized_job_output.png
│   ├── timestamp_jenkins.png
│   ├── timestamp_output.png
│   └── README.md
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
| 2 | [Variables in Jenkins](<./variables in jenkins/README.md>) | ✅ Complete |
| 3 | [Build Environments](<./build_environments/README.md>) | ✅ Complete |
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

## 🏗️ Build Environments

The Build Environment section in Jenkins provides options that control
**how a job behaves during its execution** — beyond just running the
build steps. This is where you configure things like timestamps on console
output, build timeout limits, and parametrized inputs that make a job
dynamic and reusable.

To access Build Environment options in any Jenkins job:
> **Job → Configure → Environment**

The labs in this section cover three key build environment features:

- **Add timestamps to the Console Output** — prefix every console line
  with the exact time it ran
- **Terminate a build if it's stuck** — automatically abort a build that
  exceeds a time limit
- **Build Parameters** — pass dynamic inputs into a job at trigger time
  using String, Choice, and Multi-line String parameters

---

### Part 1 — Add Timestamps to the Console Output

Timestamps add the exact clock time next to every line printed in the
Console Output. This makes it easy to see how long each step took,
identify slow steps, and correlate log output with real-world events.

**Step 1: Create the Job**

Created a new Freestyle job called `timestamp_demo`:

> Jenkins Dashboard → New Item → Enter name: `timestamp_demo` → Freestyle Project → OK

**Step 2: Enable Timestamps**

Inside `timestamp_demo → Configure → Environment`, checked the option:

> ✅ Add timestamps to the Console Output

**Step 3: Add a Build Step**

Under **Build Steps**, added an Execute shell step with the following
command:

```bash
echo "jenkins is easy to learn"
sleep 5
echo "happy learning"
```

The `sleep 5` pauses the build for 5 seconds between the two echo
statements — making the timestamps clearly show the time difference
between steps.

> 📸 *Screenshot: `timestamp_demo` Environment section with timestamps
> enabled and the shell command configured*

![Timestamp Jenkins](build_environments/timestamp_jenkins.png)

Clicked **Save** and then **Build Now**.

**Step 4: Verify the Console Output**

In the **Console Output** for Build **#1**, every line was prefixed with
the exact system clock time:

```
14:57:56 Started by user Saleem Ahmed
14:57:56 Running as SYSTEM
14:57:56 Building in workspace /var/lib/jenkins/workspace/timestamp_demo
14:57:56 [timestamp_demo] $ /bin/sh -xe /tmp/jenkins1093491002671749806.sh
14:57:57 + echo jenkins is easy to learn
14:57:57 jenkins is easy to learn
14:57:57 + sleep 5
14:58:02 + echo happy learning
14:58:02 happy learning
14:58:02 Finished: SUCCESS
```

> 📸 *Screenshot: `timestamp_demo` Build #1 Console Output — every line
> prefixed with the system clock time, sleep 5 visible between steps*

![Timestamp Output](build_environments/timestamp_output.png)

**Result:** The timestamps clearly show that `sleep 5` caused a 5-second
gap between `14:57:57` and `14:58:02` — exactly as expected. The entire
build from start to finish took 6 seconds.

---

### Part 2 — Terminate a Build if It's Stuck (Build Timeout)

The **Terminate a build if it's stuck** option automatically aborts a
build that runs longer than a defined time limit. This prevents runaway
builds from blocking the executor indefinitely — for example, a build
step that hangs waiting on a network resource or a process that never exits.

**Step 1: Create the Job**

Created a new Freestyle job called `timeout_job`:

> Jenkins Dashboard → New Item → Enter name: `timeout_job` → Freestyle Project → OK

**Step 2: Enable Build Timeout**

Inside `timeout_job → Configure → Environment`, checked the option:

> ✅ Terminate a build if it's stuck

This expanded the timeout configuration section with three fields:

| Field | Value Set | Description |
|---|---|---|
| **Time-out strategy** | `Absolute` | Aborts after a fixed number of minutes regardless of activity |
| **Timeout minutes** | `3` | The build will be forcefully terminated after 3 minutes |
| **Time-out actions** | `Fail the build` | Jenkins marks the build as FAILED when the timeout is reached |

**Step 3: Add a Build Step Designed to Time Out**

Under **Build Steps**, added an Execute shell step:

```bash
echo "hello how are you"
sleep 240
```

The `sleep 240` command pauses the build for 240 seconds (4 minutes) —
deliberately exceeding the 3-minute timeout to trigger the abort.

> 📸 *Screenshot: `timeout_job` Environment section — Terminate a build
> if it's stuck enabled, Absolute strategy, 3 minute timeout, Fail the
> build action*

![Build Timeout](build_environments/build_timeout.png)

Clicked **Save** and then **Build Now**.

**Result:** Jenkins started the build, printed `hello how are you`, then
waited on `sleep 240`. After exactly 3 minutes, Jenkins detected that
the build had exceeded its timeout, forcefully terminated it, and marked
the build as **FAILED** — without any manual intervention.

---

### Part 3 — Build Parameters

Build Parameters make a job **dynamic** — instead of hardcoding values
into the build configuration, parameters let you pass different inputs
each time the job is triggered. Jenkins supports several parameter types.
Three are demonstrated in this lab: **String**, **Choice**, and
**Multi-line String**.

To access Build Parameters in any Jenkins job:
> **Job → Configure → General → ✅ This project is parameterized**

---

#### String Parameter

A String Parameter accepts a single line of free-text input. It is the
most common parameter type — used for names, version numbers, branch
names, usernames, or any single value that changes between builds.

**Step 1: Create the Job**

Created a new Freestyle job called `parametized_demo`:

> Jenkins Dashboard → New Item → Enter name: `parametized_demo` → Freestyle Project → OK

**Step 2: Enable Parameterization**

Inside `parametized_demo → Configure → General`, checked the option:

> ✅ This project is parameterized

**Step 3: Add a String Parameter**

Clicked **Add Parameter → String Parameter** and filled in:

| Field | Value |
|---|---|
| Name | `Name` |
| Default Value | `Saleem` |

> 📸 *Screenshot: `parametized_demo` — This project is parameterized
> checked, String Parameter `Name` with default value `Saleem`*

![Parametized Job](build_environments/parametized_job.png)

**Step 4: Add a Build Step**

Under **Build Steps**, added an Execute shell step:

```bash
echo Hello how are you
echo My name is ${Name}
```

Clicked **Save** and triggered the build using **Build with Parameters**.
The `Name` field was pre-filled with `Saleem` and left unchanged.

**Step 5: Verify the Console Output**

In the **Console Output** for Build **#1**:

```
Started by user Saleem Ahmed
Running as SYSTEM
Building in workspace /var/lib/jenkins/workspace/parametized_demo
[parametized_demo] $ /bin/sh -xe /tmp/jenkins1160145879393967514357.sh
+ echo Hello how are you
Hello how are you
+ echo My name is Saleem
My name is Saleem
Finished: SUCCESS
```

> 📸 *Screenshot: `parametized_demo` Build #1 Console Output — `${Name}`
> resolved to `Saleem`, build finished with SUCCESS*

![Parametized Job Output](build_environments/parametized_job_output.png)

**Result:** Jenkins injected the `Name` parameter as an environment
variable, `${Name}` resolved to `Saleem` in the shell step, and the
build completed successfully.

---

#### Choice Parameter and Multi-line String Parameter

More complex parameter types give greater control over what values a user
can pass into a build.

**Choice Parameter** presents a dropdown menu of predefined options. The
user selects one value at trigger time — preventing free-text mistakes and
restricting inputs to only valid choices. Ideal for environment selectors,
region selectors, or any field with a known set of valid values.

**Multi-line String Parameter** accepts a block of text spanning multiple
lines. Useful for passing notes, messages, lists of items, or any input
that cannot fit on a single line.

**Step 5: Add a Choice Parameter**

Still inside `parametized_demo → Configure → General`, clicked
**Add Parameter → Choice Parameter** and filled in:

| Field | Value |
|---|---|
| Name | `environment` |
| Choices | `test` / `QA` / `Preprod` *(one per line)* |

This creates a dropdown that the user selects from when triggering the
build — choosing which environment to target.

**Step 6: Add a Multi-line String Parameter**

Clicked **Add Parameter → Multi-line String Parameter** and filled in:

| Field | Value |
|---|---|
| Name | `Mutiline` |
| Default Value | `This is my online Class` / `Hope you are doing well` / `feel free to ask any questions you may have` |

> 📸 *Screenshot: `parametized_demo` — Choice Parameter `environment`
> with choices `test`, `QA`, `Preprod` and Multi-line String Parameter
> `Mutiline` with a 3-line default value*

![Choice and Multiline Parameters](build_environments/choice_and_multiline_parameters.png)

Clicked **Save** to apply all parameters.

**Result:** When triggering `parametized_demo` with **Build with
Parameters**, Jenkins now presents three input fields — a text box for
`Name`, a dropdown for `environment` (with `test`, `QA`, `Preprod`), and
a text area for `Mutiline`. Each value is injected as an environment
variable and available throughout all build steps.

---

## 🔑 Key Lessons Learned (Build Environments)

**1. Timestamps Make Debugging Measurable**

Without timestamps, a console log is just a list of lines. With timestamps
enabled, every line carries the exact time it ran — making it trivial to
identify which step is slow, how long a build actually took, and where
time is being lost.

**2. Build Timeouts Protect the Executor**

A hung build without a timeout blocks the Jenkins executor indefinitely,
preventing all other jobs from running. Setting a timeout with `Fail the
build` as the action ensures a stuck build is detected, aborted, and
reported — freeing the executor for the next job.

**3. The Absolute Strategy Enforces a Hard Deadline**

The Absolute timeout strategy terminates a build after a fixed number of
minutes regardless of what it is doing. Set the timeout generously enough
to allow normal builds to complete, but short enough to catch genuinely
stuck ones.

**4. Parameters Eliminate Hardcoded Values**

Every value hardcoded into a job configuration is a value that must be
manually changed when requirements shift. Parameters move those values to
trigger time — making the same job reusable across environments, branches,
or any other dimension that changes between runs.

**5. Choice Parameters Enforce Valid Inputs**

A free-text String Parameter can receive any value, including typos or
invalid options. A Choice Parameter restricts the input to a predefined
list — preventing misconfigured builds before they start. Use it whenever
a field has a known, finite set of valid values.

**6. Multi-line Parameters Handle Complex Inputs**

Some build inputs cannot fit on one line — release notes, lists of
targets, or multi-step instructions. The Multi-line String Parameter
accepts a full block of text, keeping the job configuration clean while
still passing rich data into the build.

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
✅ Build Environments — Timestamps, Build Timeout, and Build Parameters documented  
⬜ Build Pipeline  
⬜ Deploy Artifacts to Tomcat Server  
⬜ Deploy Build to Tomcat Server using Jenkins  
⬜ Declarative Pipeline  

---

## 🔗 Connect

[LinkedIn](https://www.linkedin.com/in/saleemfai) · 📍 Cameroon
