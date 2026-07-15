# 🌱 TerraWeek Day 1 — Introduction to IaC & Terraform Basics

Today was Day 1 of TerraWeek. Goal was simple: understand *why* Infrastructure as Code exists, get Terraform installed, learn the basic words people throw around, and run a real `terraform apply` without needing a cloud account.

---

## Task 1: Understand IaC & Terraform

### What is Infrastructure as Code, and what problems does it solve?

Infrastructure as Code (IaC) means you describe your servers, networks, storage, and other infra in files — not by clicking through a cloud console every time.

The console works fine when you're learning or spinning up one box. It falls apart when you need the same setup again, when a teammate has to recreate what you built from memory, or when you want a reviewable history of *what changed and why*. ClickOps is hard to audit, easy to mess up, and nearly impossible to reproduce cleanly.

IaC fixes that by treating infra like software: version-controlled, reviewable, repeatable. You write what you want, run a tool, and the environment matches the code. Same config on day 1 and day 100. Same config for dev and prod (with different variables). No more "it works on my account."

### What is Terraform, and why is it popular?

Terraform is HashiCorp's tool for defining and managing infrastructure with code. You write `.tf` files that declare the end state you want, and Terraform figures out how to get there.

A few reasons it stuck:

- **Declarative** — you say *what* you want (an S3 bucket with versioning on), not a long list of API calls in order.
- **Provider-agnostic** — same workflow for AWS, Azure, GCP, Docker, Kubernetes, GitHub, and thousands of other platforms.
- **Huge ecosystem** — modules, providers, and examples for almost anything. If a platform has an API, someone has probably written a Terraform provider for it.
- **Plan before apply** — you always get a preview of changes before anything is created or destroyed. That safety net matters a lot in production.

### Terraform vs the alternatives (one line each)

| Tool | How it compares |
|------|-----------------|
| **OpenTofu** | Community fork of Terraform under a fully open-source license (MPL); mostly drop-in compatible with HCL and state, so existing Terraform code usually just works. |
| **Pulumi** | Same IaC idea, but you write real programming languages (TypeScript, Python, Go, etc.) instead of HCL — nicer if your team already lives in those languages. |
| **CloudFormation** | AWS-native IaC (YAML/JSON). Solid if you're all-in on AWS, but you don't get multi-cloud the way Terraform does. |
| **Ansible** | Great for configuring servers and running procedures; it's more procedural and often used *alongside* Terraform rather than as a full replacement for cloud provisioning. |

---

## Task 2: Install Terraform (latest version)

Installed **Terraform v1.15.8** (latest stable in the 1.15 line as of today).

### Verify

```bash
vishakha@Vishakha:~$ terraform version
Terraform v1.15.8
on linux_amd64
vishakha@Vishakha:~$ terraform -help
Usage: terraform [global options] <subcommand> [args]

The available commands for execution are listed below.
The primary workflow commands are given first, followed by
less common or more advanced commands.

Main commands:
  init          Prepare your working directory for other commands
  validate      Check whether the configuration is valid
  plan          Show changes required by the current configuration
  apply         Create or update infrastructure
  destroy       Destroy previously-created infrastructure

All other commands:
  console       Try Terraform expressions at an interactive command prompt
  fmt           Reformat your configuration in the standard style
  force-unlock  Release a stuck lock on the current workspace
  get           Install or upgrade remote Terraform modules
  graph         Generate a Graphviz graph of the steps in an operation
  import        Associate existing infrastructure with a Terraform resource
  login         Obtain and save credentials for a remote host
  logout        Remove locally-stored credentials for a remote host
  metadata      Metadata related commands
  modules       Show all declared modules in a working directory
  output        Show output values from your root module
  providers     Show the providers required for this configuration
  query         Search and list remote infrastructure with Terraform
  refresh       Update the state to match remote systems
  show          Show the current state or a saved plan
  stacks        Manage HCP Terraform stack operations
  state         Advanced state management
  taint         Mark a resource instance as not fully functional
  test          Execute integration tests for Terraform modules
  untaint       Remove the 'tainted' state from a resource instance
  version       Show the current Terraform version
  workspace     Workspace management

Global options (use these before the subcommand, if any):
  -chdir=DIR    Switch to a different working directory before executing the
                given subcommand.
  -help         Show this help output or the help for a specified subcommand.
  -version      An alias for the "version" subcommand.
```

Also installed the **HashiCorp Terraform** extension in VS Code for syntax highlighting, formatting, and autocomplete — makes writing HCL much less painful.

<img width="940" height="499" alt="image" src="https://github.com/user-attachments/assets/9f8105b6-be54-49a4-9cef-a6cf13fadf1b" />

---

## Task 3: Learn 6 Crucial Terraform Terminologies

| Terminologies | Description | Example |
| --- | --- | --- |
| **Provider** | The plugin that teaches Terraform how to talk to a specific platform. | The `hashicorp/aws` provider lets you create EC2 instances; `hashicorp/local` lets you create files on your machine. |
| **Resource** | One thing Terraform manages — a server, a bucket, a DNS record, even a local file. | `resource "local_file" "greeting" { ... }` creates a file named `greeting.txt`. |
| **State** | Terraform's memory of what it already created and how those things map to your config. Stored in `terraform.tfstate` (or a remote backend). | After apply, the state file knows that `random_pet.name` currently has id `allowed-cattle`. |
| **Plan** | A dry-run preview of what Terraform *would* change if you applied right now — add, change, or destroy. | `terraform plan` said "2 to add, 0 to change, 0 to destroy" before I created anything. |
| **HCL** | HashiCorp Configuration Language — the readable syntax you write Terraform configs in (those `.tf` files). | Blocks like `resource "random_pet" "name" { length = 2 }` are HCL. |
| **Module** | A reusable package of Terraform config you can call like a function, instead of copy-pasting the same resources everywhere. | A `vpc` module that takes a CIDR and returns a ready network — used once for dev, once for prod. |

---

## Task 4: First Terraform config (no cloud needed)

Used the starter code from `day01/example`. It only uses the `local` and `random` providers, so:

- No AWS/Azure/GCP account  
- No credentials  
- Zero cost  

### Running the core workflow

```bash
cd example
terraform init      # download providers, set up the working directory
terraform fmt       # format the code (already clean — no changes)
terraform validate  # Success! The configuration is valid.
terraform plan      # Plan: 2 to add, 0 to change, 0 to destroy.
terraform apply     # type yes — creates the resources
cat greeting.txt
terraform destroy   # type yes — tears everything down
```

### What I actually got

**1️⃣ Initialize Terraform**

<img width="940" height="594" alt="image" src="https://github.com/user-attachments/assets/7977c263-befa-4613-afad-35f1d28190a6" />

**2️⃣ Format and Validate the configuration**

<img width="810" height="201" alt="image" src="https://github.com/user-attachments/assets/6fd56229-a622-438b-88fa-c2a812f9597f" />

**3️⃣ Preview infrastructure changes**

<img width="940" height="538" alt="image" src="https://github.com/user-attachments/assets/7674b8d3-2e4f-4f89-8518-022deaad8832" />

**4️⃣ Apply configurations**

<img width="940" height="671" alt="image" src="https://github.com/user-attachments/assets/397acc4b-b118-43db-855f-0a15e1122064" />

**5️⃣ Verify output and destroy the resources**

<img width="940" height="594" alt="image" src="https://github.com/user-attachments/assets/57626af0-3078-43df-9a15-f3f5ef68982b" />

---

## The core Terraform workflow (what Day 1 drilled into me)

```
  Write  ──▶  Init  ──▶  Plan  ──▶  Apply  ──▶  Destroy
  (.tf)      (setup)    (preview)  (create)    (clean up)
```

1. **Write** — describe the desired state in HCL  
2. **Init** — download providers/modules, prepare the directory  
3. **Plan** — see the diff before anything changes  
4. **Apply** — make reality match the code  
5. **Destroy** — remove everything Terraform manages (when you're done)

That loop is basically the whole game. Everything else is details on top of it.

---

## Bonus (Brownie Points)

### Tab completion
```bash
terraform -install-autocomplete
```
Restart the shell after. Tab completion for subcommands is a small quality-of-life win.

<img width="940" height="607" alt="image" src="https://github.com/user-attachments/assets/5557b769-aa37-4d29-b6d6-40c9a603cc2d" />

### What is `.terraform.lock.hcl`?
It's the dependency lock file. When `terraform init` picks specific provider versions (and their checksums), it records them here so everyone on the team — and every CI run — gets the *same* provider binaries. Commit this file to git. Don't commit `.terraform/` or `*.tfstate`.

<img width="940" height="742" alt="image" src="https://github.com/user-attachments/assets/7d84be12-51fe-4561-b823-277042d92920" />

### OpenTofu (quick look)
OpenTofu is the open-source fork of Terraform. Commands look almost identical (`tofu init`, `tofu plan`, `tofu apply`). For this local example it behaves the same. Main difference is licensing and governance — OpenTofu is fully open source under the Linux Foundation / CNCF umbrella, while Terraform itself is under HashiCorp's BSL. Worth knowing if your org cares about license terms.

Referred [OpenTofu Installation](https://opentofu.org/docs/intro/install/deb/) link to install OpenTofu.

<img width="940" height="568" alt="image" src="https://github.com/user-attachments/assets/94886e9c-48e6-4727-a335-4101694d8c5c" />

---

## What I learned today (short version)

- IaC exists because clicking in a console doesn't scale, doesn't review well, and doesn't reproduce cleanly.
- Terraform is popular because it's declarative, multi-cloud, and has a plan step that keeps you from flying blind.
- The vocabulary (provider, resource, state, plan, HCL, module) shows up in every tutorial — get comfortable with it early.
- You don't need a cloud account to start. Local + random providers are enough to feel the full workflow.
- Always `plan` before `apply`. Always think about state. Always clean up with `destroy` when you're done learning.
