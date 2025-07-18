Here's a **detailed yet easy explanation of RBAC (Role-Based Access Control) in Jenkins**, including **real-time use cases**, **setup**, and **best practices** — perfect for understanding or implementing it in a production environment.

---

# 🔐 Jenkins RBAC – Role-Based Access Control (Complete Guide)

---

## 🚨 What is RBAC?

**RBAC (Role-Based Access Control)** in Jenkins allows you to **assign roles to users or teams** and define what each role **can or cannot do** — based on the principle of **least privilege**.

---

## 🎯 Why is RBAC Needed?

Imagine this:

* Devs should only trigger builds ✅
* QA should only view results, not change jobs ✅
* Admins need full control ✅
* Interns must only view 1 job ✅

Without RBAC, Jenkins either gives **too much access** or **too little**.

---

## 🧱 Types of Access Control in Jenkins

1. **Matrix-Based Security** (built-in, powerful, manual)
2. **Project-based Matrix Authorization**
3. ✅ **Role-Based Strategy Plugin** (**RBAC plugin**) – easiest and flexible way
4. **OpenID, LDAP, SSO integration** (for enterprise RBAC)

We'll focus on **Role-Based Strategy Plugin**, which is the real-time RBAC most teams use.

---

## ✅ Step-by-Step: Setup RBAC in Jenkins

### 🔌 Step 1: Install the RBAC Plugin

* Go to: `Manage Jenkins → Manage Plugins`
* Search for: `Role-based Authorization Strategy`
* ✅ Install and restart Jenkins

---

### 🛠️ Step 2: Configure Global Security

* Go to: `Manage Jenkins → Configure Global Security`
* Under **Authorization**, select:

  ```
  Role-Based Strategy
  ```

---

### 👥 Step 3: Define Roles

Go to:
`Manage Jenkins → Manage and Assign Roles → Manage Roles`

#### Define 2 types of roles:

#### ➤ 1. **Global Roles** (entire Jenkins)

| Role Name | Permissions                |
| --------- | -------------------------- |
| admin     | All permissions            |
| developer | Read, Job Build, Workspace |
| viewer    | Job Read only              |

#### ➤ 2. **Project Roles** (specific jobs/pipelines)

| Role Name    | Pattern      | Permissions     |
| ------------ | ------------ | --------------- |
| qa\_tester   | `QA-.*`      | Job Read, Build |
| intern\_view | `Intern-Job` | Job Read only   |

🧠 The **pattern** is a regex that matches job names.

---

### 🧑‍🤝‍🧑 Step 4: Assign Roles to Users

Go to:
`Manage Jenkins → Manage and Assign Roles → Assign Roles`

You’ll see:

| User/Group | Global Roles | Project Roles |
| ---------- | ------------ | ------------- |
| alice      | developer    | qa\_tester    |
| bob        | viewer       | intern\_view  |
| adminuser  | admin        | (all)         |

You can assign multiple roles to a user.

---

## 👨‍💻 Real-Time Use Cases

### 🧪 Example 1: QA Team Role

* **Job Prefix**: `QA-*`
* **Role**: `qa_tester`
* **Permissions**:

  * Job: Read ✅
  * Job: Build ✅
  * Configure ❌
  * Delete ❌

✅ Result: QA team can run tests but **not break job config**.

---

### 🔧 Example 2: Dev Role

* **Access**: Can only build and view their own jobs.
* **Permissions**: Read, Build, Workspace
* Assigned to projects: `Dev-*`

✅ Devs have access only to their pipelines.

---

### 👀 Example 3: Intern Role

* View-only role on `Intern-Job`
* No build or config access

✅ Interns can watch builds, learn Jenkins, but **can’t click Build or Modify anything**.

---

## 🧩 Bonus: Integrate with GitHub/LDAP

In real companies:

* Use **GitHub OAuth**, **Active Directory**, or **LDAP** for login
* Then assign RBAC roles to users **based on their GitHub Teams or LDAP groups**

This way:

* **Dev Team GitHub group** → `developer` role
* **Admin LDAP group** → `admin` role
* Managed at **organization level**, not manually

---

## 🛡️ Best Practices for RBAC

| Tip                 | Description                                            |
| ------------------- | ------------------------------------------------------ |
| 🔒 Least Privilege  | Give minimum access needed for each user/team          |
| 👥 Use Groups       | Use user groups instead of individual assignments      |
| ✅ Audit Roles       | Review roles and permissions regularly                 |
| 🧪 Use Job Patterns | Use regex patterns (`Dev-*`, `QA-*`) to control access |
| 💡 Document It      | Keep a README or access matrix for team clarity        |

---

## 📄 Summary

| Component          | Explanation                                          |
| ------------------ | ---------------------------------------------------- |
| Plugin             | `Role-based Authorization Strategy` plugin           |
| Roles              | Global, Project, Folder                              |
| Assignments        | Map users → roles (with permissions)                 |
| Real-world Example | Dev = build only, QA = test only, Intern = read-only |
| Auth integration   | Combine with GitHub OAuth, LDAP, SSO                 |

---

Would you like a **diagram + sample configuration matrix** or a downloadable `README.md` for documentation?
