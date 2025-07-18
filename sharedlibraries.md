Here’s a **simple and clear explanation of Jenkins Shared Libraries** — made easy for you to understand and use in real projects.

---

# 📚 Jenkins Shared Libraries — Easy & Detailed Guide

---

## 🧠 What is a Shared Library?

A **Jenkins Shared Library** is a **central place to store reusable pipeline code**.
Instead of writing the same `pipeline` or `script blocks` in every Jenkinsfile, you write them **once** in a shared library and reuse it in many jobs/projects.

---

### ✅ Why Use Shared Libraries?

| Problem                    | Shared Library Solution                 |
| -------------------------- | --------------------------------------- |
| Repeating same steps       | Write once and reuse across projects    |
| Long Jenkinsfiles          | Keep Jenkinsfiles clean and short       |
| Difficult maintenance      | Change one file in library = update all |
| CI/CD across multiple apps | Share common build, test, deploy logic  |

---

## 📁 Structure of Shared Library

The library lives in a **Git repository** with a special structure:

```
(root)
├── vars/
│   └── sayHello.groovy        <-- simple global methods
├── src/
│   └── org/foo/Utils.groovy   <-- custom classes
├── resources/
│   └── email/template.txt     <-- templates or static files
└── README.md
```

---

### 🗂️ Folder Explanation:

| Folder       | Purpose                                | Example                         |
| ------------ | -------------------------------------- | ------------------------------- |
| `vars/`      | Global reusable methods (easy to call) | `sayHello()` in Jenkinsfile     |
| `src/`       | Reusable classes and helpers           | `Utils.buildApp()`              |
| `resources/` | Store templates, YAMLs, configs, etc.  | Email templates, kube manifests |

---

## ✨ Example 1: Create a Simple Shared Library

### ➤ File: `vars/sayHello.groovy`

```groovy
def call(String name = 'Stranger') {
    echo "👋 Hello, ${name}!"
}
```

> 🔹 `call()` makes it usable like a function: `sayHello("Mounisha")`

---

### ➤ File: `src/org/demo/Utils.groovy`

```groovy
package org.demo

class Utils implements Serializable {
    def steps

    Utils(steps) {
        this.steps = steps
    }

    def greet(name) {
        steps.echo "Hello from Utils: ${name}"
    }
}
```

---

## ⚙️ How to Use in Jenkinsfile

### ➤ Step 1: **Add Shared Library in Jenkins**

Go to:

**Jenkins → Manage Jenkins → Configure System → Global Pipeline Libraries**

* **Name**: `my-shared-lib`
* **Default version**: `main` or `master`
* **Retrieval method**: Modern SCM
* **Git repo**: `https://github.com/your-org/your-shared-library.git`

---

### ➤ Step 2: **Use in Jenkinsfile**

```groovy
@Library('my-shared-lib') _

pipeline {
    agent any

    stages {
        stage('Simple Greet') {
            steps {
                sayHello('Mounisha') // uses vars/sayHello.groovy
            }
        }

        stage('Use Utils Class') {
            steps {
                script {
                    def util = new org.demo.Utils(this)
                    util.greet('Chirapureddy')
                }
            }
        }
    }
}
```

---

## ✅ Benefits Recap

✅ Reuse code across projects
✅ Clean and short Jenkinsfiles
✅ Easy to test & maintain logic
✅ Central version control

---

## 🧪 Bonus: Testing Shared Library

You can write unit tests using [`JenkinsPipelineUnit`](https://github.com/jenkinsci/JenkinsPipelineUnit)

---

## 🔚 Final Thoughts

Use Shared Libraries when:

* You have **multiple Jenkinsfiles** doing similar things
* You want to follow **DRY (Don’t Repeat Yourself)** principle
* You want to **standardize pipelines** across teams

---

Would you like a working example GitHub repo + downloadable `README.md` for this?
