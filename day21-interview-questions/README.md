# Terraform Interview Questions (Basic → Advanced → Scenario-Based)

A complete set of Terraform interview questions to help you prepare — organized by difficulty level, ending with real-world scenario-based questions.

---

## 🟢 Basic Level

1. **What is Terraform and how is it different from other IaC tools (e.g., CloudFormation, Ansible)?**
   Terraform is an open-source Infrastructure as Code (IaC) tool by HashiCorp used to provision and manage cloud/on-prem infrastructure declaratively using HCL (HashiCorp Configuration Language). Unlike CloudFormation (AWS-only), Terraform is cloud-agnostic. Unlike Ansible (procedural, configuration management focused), Terraform is declarative and focused on provisioning.

2. **What are the main components of Terraform?**
   - Providers
   - Resources
   - Variables
   - Outputs
   - State file
   - Modules
   - Provisioners
   - Data sources

3. **What is a provider in Terraform?**
   A plugin that lets Terraform interact with APIs of cloud platforms (AWS, Azure, GCP) or other services.

4. **What is the purpose of `terraform init`?**
   Initializes a working directory: downloads providers/modules and sets up the backend.

5. **What is the difference between `terraform plan` and `terraform apply`?**
   `plan` shows the execution plan (what will change) without making changes. `apply` executes the plan and makes actual changes to infrastructure.

6. **What is the Terraform state file (`terraform.tfstate`)?**
   A JSON file that stores the current state of managed infrastructure, mapping real-world resources to your configuration.

7. **What is the difference between `terraform destroy` and manually deleting resources?**
   `terraform destroy` removes resources tracked in state in a controlled, dependency-aware order. Manual deletion causes state drift.

8. **What are variables in Terraform and how do you define them?**
   Inputs to make configurations reusable and dynamic, defined via `variable` blocks and set via `.tfvars`, CLI flags, or environment variables.

9. **What is the difference between a variable and a local value?**
   Variables are external inputs; locals are internal, computed values used to avoid repetition within a configuration.

10. **What is an output value used for?**
    To expose specific values (like resource IDs, IPs) after apply, useful for chaining modules or displaying info.

---

## 🟡 Intermediate Level

11. **What is the difference between `terraform.tfstate` and `terraform.tfstate.backup`?**
    The backup file is an automatic copy of the previous state, created before an update, for recovery purposes.

12. **What is a remote backend, and why use one?**
    Remote backends (S3, Terraform Cloud, Azure Blob) store state remotely, enabling team collaboration, locking, and preventing local state loss.

13. **What is state locking and why is it important?**
    Prevents concurrent operations from corrupting the state file. Commonly implemented via DynamoDB (with S3 backend) to avoid race conditions.

14. **What are Terraform modules and why are they useful?**
    Reusable, self-contained packages of Terraform configuration used to organize and reuse infrastructure code across projects/environments.

15. **What is the difference between `count` and `for_each`?**
    - `count`: creates N instances of a resource based on an index number.
    - `for_each`: creates resources based on a map or set, giving more meaningful keys and better handling when items are added/removed (avoids the shifting-index problem `count` has).

16. **What are data sources in Terraform?**
    Read-only blocks used to fetch information about existing infrastructure not managed by the current configuration (e.g., an existing VPC ID).

17. **What is the difference between a resource and a data source?**
    A resource creates/manages infrastructure; a data source only reads existing information.

18. **What are provisioners and when should you avoid them?**
    Provisioners (`local-exec`, `remote-exec`) run scripts on resource creation/destruction. HashiCorp recommends avoiding them where possible — prefer native tooling (e.g., cloud-init, Ansible) since provisioners aren't tracked well and can cause unpredictable behavior.

19. **What is Terraform workspace, and when would you use it?**
    Workspaces allow multiple state files for the same configuration (e.g., dev/staging/prod) without duplicating code — useful for lightweight environment separation, though many teams prefer separate directories/backends for stronger isolation.

20. **What is the `depends_on` argument used for?**
    Explicitly defines a dependency between resources when Terraform can't automatically infer it from configuration references.

21. **Explain Terraform's dependency graph.**
    Terraform builds a Directed Acyclic Graph (DAG) of all resources based on references, determining the correct order for create/update/destroy operations.

22. **What is a `.tfvars` file?**
    A file used to define variable values separately from the main configuration, useful for environment-specific values (e.g., `dev.tfvars`, `prod.tfvars`).

23. **What is the lifecycle block, and what are its common arguments?**
    Controls resource behavior:
    - `create_before_destroy`
    - `prevent_destroy`
    - `ignore_changes`

24. **What is drift in Terraform, and how do you detect it?**
    Drift occurs when real infrastructure changes outside Terraform (manual console changes). Detected using `terraform plan` (shows differences) or `terraform refresh`.

---

## 🔴 Advanced Level

25. **How does Terraform handle state file conflicts in a team environment?**
    Via remote backends with locking (e.g., S3 + DynamoDB, Terraform Cloud) — only one `apply` can run at a time; others wait or fail gracefully.

26. **What is the difference between `terraform import` and writing resources manually?**
    `terraform import` brings existing infrastructure under Terraform management by mapping it into the state file — but you must still manually write matching configuration, since import doesn't generate `.tf` code (in versions prior to `terraform plan -generate-config-out`).

27. **What are dynamic blocks, and when would you use them?**
    Used to dynamically generate repeatable nested blocks (like `ingress`/`egress` rules in a security group) based on a variable list/map, reducing repetitive code.

28. **Explain Terraform's meta-arguments.**
    Special arguments usable with any resource: `count`, `for_each`, `provider`, `lifecycle`, `depends_on`.

29. **How would you manage secrets in Terraform (e.g., DB passwords)?**
    - Avoid hardcoding in `.tf` files
    - Use environment variables or `.tfvars` (excluded from VCS)
    - Use a secrets manager (AWS Secrets Manager, HashiCorp Vault) with data sources
    - Mark variables as `sensitive = true` to prevent them showing in CLI output

30. **What does `sensitive = true` actually do (and not do)?**
    It hides the value from CLI output/logs, but the value is still stored in plaintext in the state file — so state file security (encryption, access control) is still essential.

31. **How do you structure Terraform code for multiple environments (dev/staging/prod)?**
    Common approaches:
    - Separate directories per environment with shared modules
    - Workspaces with environment-specific `.tfvars`
    - Separate state files/backends per environment (safer, avoids blast radius across environments)

32. **What is the purpose of the `terraform state` command family (e.g., `state mv`, `state rm`)?**
    Used to manually manipulate the state file — e.g., renaming a resource without destroying/recreating it (`state mv`), or removing a resource from state without deleting the actual infrastructure (`state rm`).

33. **How does Terraform determine what needs to change during `plan`?**
    It compares the desired configuration against the current state (and optionally refreshes against real infrastructure) to compute a diff, then builds an execution plan respecting the dependency graph.

34. **What happens if the state file is lost?**
    Terraform loses track of managed resources. You'd need to either restore from a backup/remote state history, or re-import each resource manually using `terraform import`.

35. **What are Terraform providers' version constraints, and why are they important?**
    Defined in `required_providers` blocks to pin provider versions (e.g., `~> 5.0`), preventing unexpected breaking changes when providers release new versions.

36. **What is the difference between `terraform validate` and `terraform plan`?**
    `validate` checks syntax and internal consistency without accessing remote state/APIs. `plan` requires provider/API access and produces an actual change plan.

---

## 🎯 Scenario-Based Questions

37. **Scenario:** *Your team applied changes to production, and now the state file shows drift because someone manually deleted a resource in the AWS Console. How do you handle this?*
    Run `terraform plan` to detect the drift. Decide whether to:
    - Re-apply to recreate the resource (if it should exist), or
    - Remove it from state (`terraform state rm`) if it was intentionally deleted, then update the configuration to match reality.
    Long-term: restrict console access and enforce changes only via Terraform (via IAM policies/SCPs).

38. **Scenario:** *Two engineers run `terraform apply` at the same time on the same state file. What happens, and how do you prevent it?*
    Without locking, this can corrupt the state file. Using a remote backend like S3 with DynamoDB locking (or Terraform Cloud) ensures the second `apply` waits or fails safely instead of corrupting state.

39. **Scenario:** *You need to rename a resource in your configuration (e.g., `aws_instance.web` → `aws_instance.app`) without destroying and recreating it. How?*
    Use `terraform state mv aws_instance.web aws_instance.app` to update the state mapping, then update the configuration file to match the new resource name.

40. **Scenario:** *You accidentally hardcoded a secret (like an API key) into a `.tf` file and pushed it to GitHub. What's your remediation process?*
    - Immediately rotate/revoke the exposed secret
    - Remove it from the codebase and git history (e.g., `git filter-repo` or BFG Repo-Cleaner)
    - Move secrets to a secure manager (Vault/Secrets Manager) with `sensitive = true` variables going forward
    - Add `.tfvars` and secret files to `.gitignore`

41. **Scenario:** *You need to migrate an existing manually-created AWS S3 bucket into Terraform management without recreating it. Steps?*
    - Write a matching `resource "aws_s3_bucket"` block in your `.tf` file
    - Run `terraform import aws_s3_bucket.my_bucket <bucket-name>`
    - Run `terraform plan` to confirm no unwanted changes are detected (adjust config until plan shows no diff)

42. **Scenario:** *Your `terraform apply` is trying to destroy and recreate a resource that shouldn't be recreated (e.g., a database), causing potential data loss. How do you prevent this?*
    Use the `lifecycle { prevent_destroy = true }` block on the resource to block destructive operations, and investigate why Terraform wants to recreate it (often due to an `ForceNew` attribute change — check the plan output for the specific attribute causing replacement).

43. **Scenario:** *You have 10 identical S3 buckets to create, differing only by name. Would you use `count` or `for_each`, and why?*
    `for_each` with a set/map of names is preferred — it's more resilient to changes in the list (removing a bucket from the middle of a `count`-based list would cause Terraform to want to destroy/recreate unrelated buckets due to index shifting).

44. **Scenario:** *Your CI/CD pipeline needs to run `terraform apply` automatically, but you want a human approval step before production changes. How would you design this?*
    - Run `terraform plan -out=tfplan` and save/output the plan as a build artifact
    - Require manual approval (e.g., a GitHub Actions "environment protection rule" or Jenkins input step) before proceeding
    - Run `terraform apply tfplan` only after approval, ensuring the exact reviewed plan is applied (not a fresh, possibly different plan)

45. **Scenario:** *You want to enforce that certain tags (like `Owner`, `Environment`) exist on every resource across your organization. How would you approach this in Terraform?*
    - Use `default_tags` at the provider level (for AWS) to apply tags globally
    - Use policy-as-code tools like **Sentinel** (Terraform Cloud/Enterprise) or **Open Policy Agent (OPA)/Checkov** in CI to enforce tagging policies before apply

46. **Scenario:** *Your Terraform state file has grown huge and `plan`/`apply` are slow across a monolithic configuration managing hundreds of resources. How do you improve this?*
    - Split the configuration into smaller, independently-managed state files (per service/environment)
    - Use `-target` sparingly for quick fixes (not as a long-term strategy)
    - Use modules to organize code while keeping state separated logically
    - Consider `terraform state` data sources (`terraform_remote_state`) to share outputs between separately-managed states

---

## 🔥 Real-World Crisis / Experience-Based Scenarios

47. **Scenario:** *You lost your Terraform state file AND deleted the entire code repository. No backups. What do you do immediately? You log into the AWS console and see 50+ resources running — how do you identify which are prod resources and which are dev resources?*

    **Immediate actions:**
    - Stay calm — do **not** delete or modify anything in the console yet. Freeze all further Terraform runs across the team so no one else touches state.
    - Check every possible backup source before assuming total loss:
      - Remote backend versioning (if S3 was used, check S3 **versioning** — a previous version of `terraform.tfstate` may still exist even after "deletion")
      - Terraform Cloud/Enterprise state history (it keeps run/state history automatically)
      - CI/CD pipeline logs/artifacts — plan/apply logs sometimes contain resource IDs
      - Local machines of teammates — someone may have an old `.tfstate` or a stale git clone
      - `git reflog` / GitHub's own history (deleted repos can sometimes be recovered by GitHub support within a short window) — contact GitHub support immediately
    - If truly nothing is recoverable, treat this as a **full re-discovery and re-import** exercise.

    **Identifying prod vs dev resources in the console (50+ resources, no state/code):**
    - **Tags** — this is the #1 method. Well-run environments tag everything with `Environment=prod/dev`, `Owner`, `Project`. Filter AWS Console/Resource Groups Tag Editor by tag.
    - **Naming conventions** — check resource names/prefixes (e.g., `prod-web-`, `dev-api-`) if tagging wasn't enforced.
    - **AWS Resource Groups & Tag Editor** — use this single service to search across all resource types by tag/name at once instead of checking each service individually.
    - **VPC/subnet/account separation** — if prod and dev live in separate AWS accounts or VPCs (best practice), this alone answers the question.
    - **CloudTrail logs** — check who created each resource and when, to correlate with known deployment dates/emails.
    - **Traffic/usage patterns** — check CloudWatch metrics; prod resources typically show real traffic, dev/test resources show sporadic or no traffic.
    - **Cost Explorer / Billing tags** — cost allocation tags often reveal environment and team ownership.

    **Long-term fix:** Enforce mandatory tagging via Service Control Policies (SCPs) or `default_tags` in the provider block, enable S3 versioning + MFA delete on the state bucket, use remote state with locking, and require infra changes to go through a CI/CD pipeline (never local `apply`) so history is always in git + pipeline logs — never a single point of failure.

---

48. **Scenario:** *You're creating an EC2 instance, but `terraform plan` shows that an S3 bucket will be deleted. How do you debug this?*

    **Step-by-step debugging approach:**
    1. **Read the plan output carefully first** — Terraform shows *why* it wants to destroy a resource (`~ update in-place`, `-/+ destroy and re-create`, or `- destroy`). Check if it's an unrelated, unintended deletion or a forced replacement.
    2. **Check for configuration drift** — did someone manually modify or partially delete the S3 bucket outside Terraform? Run `terraform state show aws_s3_bucket.<name>` and compare against real AWS console state.
    3. **Check if the resource was accidentally removed from the `.tf` file** — if the `resource "aws_s3_bucket"` block was deleted or commented out (maybe during a merge conflict resolution or copy-paste error while adding the EC2 code), Terraform will plan to destroy it since it's no longer in configuration but still in state.
    4. **Check `count`/`for_each` index shifts** — if the bucket was defined using `count` and an earlier item in the list was removed, indexes shift and Terraform may see it as a different resource to destroy and recreate.
    5. **Check for a state mismatch between branches/workspaces** — verify you're pointed at the correct `terraform workspace` and backend/state file; someone may have applied a different branch's state.
    6. **Look for module source/version changes** — if the S3 bucket was defined in a module and the module version changed, resource addressing inside the module can change, causing Terraform to see it as a "new" resource (destroy old, create new).
    7. **Run `terraform plan -target=aws_s3_bucket.<name>`** in isolation to confirm the destroy is specific to that resource and not a cascading dependency issue.

    **Immediate safeguard:** Add `lifecycle { prevent_destroy = true }` to the S3 bucket resource so `apply` fails safely instead of deleting it while you investigate. Never blindly run `apply` when `plan` shows an unexpected destroy — always pause and understand the "why" first.

---

49. **Scenario:** *Describe a Terraform module you created. How do you maintain it?*

    **Sample answer structure (customize with your real experience):**

    *Example:* "I created a reusable VPC module that provisions a VPC, public/private subnets across multiple AZs, an Internet Gateway, NAT Gateway(s), and route tables. It accepts inputs like `cidr_block`, `az_count`, `environment`, and `enable_nat_gateway`, and outputs the VPC ID, subnet IDs, and route table IDs for downstream modules (like EC2 or RDS modules) to consume."

    **How I maintain it:**
    - **Version control & tagging** — the module lives in its own Git repo (or a `modules/` folder), tagged with semantic version releases (`v1.0.0`, `v1.1.0`) so consuming projects can pin a specific version via `source = "git::...?ref=v1.2.0"` and upgrade deliberately.
    - **Input validation** — used `variable` blocks with `validation` rules to catch bad inputs early (e.g., ensuring CIDR format is valid) rather than failing mid-apply.
    - **Documentation** — maintained a `README.md` per module (inputs, outputs, usage example) — often auto-generated with `terraform-docs`.
    - **Testing** — used `terraform validate`, `terraform plan` in CI on every PR, and tools like `terratest` or `checkov`/`tflint` for static analysis and policy checks before merging.
    - **Backward compatibility** — avoided breaking changes to input/output names where possible; when a breaking change was unavoidable, bumped the major version and documented a migration guide.
    - **Change process** — changes go through PR review, CI plan output review, and are tested in a dev environment before being adopted in staging/prod consuming projects.

---

50. **Scenario:** *State Lock Crisis Timeline:*
    ```
    2:00 PM: You start terraform apply (security group rules)
    2:01 PM: Team member starts terraform apply (EC2 instance)
    2:02 PM: You get lock error
    2:35 PM: Still locked. Team member went on leave.
    Network issue kept lock active.
    You can't force-unlock.
    Production is BLOCKED.
    Manager asking: When will this be fixed?
    ```

    **Immediate response (communicate first):**
    - Tell the manager: *"State is locked due to a network interruption during a colleague's apply. I'm verifying it's safe to release the lock, then unblocking — ETA ~10-15 minutes."* Don't stay silent under pressure; give a time-boxed update.

    **Technical resolution steps:**
    1. **Confirm no process is actually still running.** Check if the team member's machine/CI job is truly dead (not just slow) — ask if they're reachable, or check the CI/CD job status if it ran through a pipeline rather than locally.
    2. **Identify the lock details** — the lock error output includes the Lock ID, who/what created it, and the timestamp. If using S3 + DynamoDB backend, check the DynamoDB lock table directly (`aws dynamodb get-item --table-name <lock-table> --key '{"LockID": {"S": "<lock-id>"}}'`) to confirm it's stale.
    3. **If confirmed stale/orphaned, force-unlock:**
       ```bash
       terraform force-unlock <LOCK_ID>
       ```
       (If "can't force-unlock" means a permissions issue — check IAM permissions for `dynamodb:DeleteItem` on the lock table, not that the command doesn't exist.)
    4. **If force-unlock via CLI fails due to permissions/access**, manually delete the stale lock entry directly from the DynamoDB table (with a second person confirming, since this bypasses Terraform's safety check) — only after confirming absolutely no apply is genuinely still in progress.
    5. **Re-run `terraform plan`** first (never jump straight to `apply`) to confirm state integrity wasn't corrupted by the interrupted apply, before proceeding.

    **Root cause fix (prevent recurrence):**
    - Ensure **all** `apply` operations run through CI/CD (not local machines) — this avoids locks surviving a laptop going to sleep, VPN dropping, or someone leaving for the day mid-run.
    - Add monitoring/alerting on the DynamoDB lock table so long-held locks trigger an alert instead of being discovered by a blocked teammate.
    - Document a clear "how to safely force-unlock" runbook so this isn't a fire-drill next time.

---

51. **Scenario:** *How do you maintain Terraform version consistency across 15 projects and 4 developers?*

    **Approach:**
    1. **Pin the Terraform CLI version in every project** using a `required_version` constraint in the `terraform {}` block:
       ```hcl
       terraform {
         required_version = "= 1.7.5"
       }
       ```
       Using `=` (exact pin) rather than `>=` avoids "works on my machine" drift between developers on different versions.

    2. **Pin provider versions too**, not just Terraform core:
       ```hcl
       required_providers {
         aws = {
           source  = "hashicorp/aws"
           version = "= 5.42.0"
         }
       }
       ```

    3. **Use a version manager** like `tfenv` (similar to `nvm` for Node) so each developer/project can install and switch to the exact required version locally with one command (`tfenv install`, `tfenv use`).

    4. **Centralize the version in a single source of truth** — e.g., a `.terraform-version` file per project (which `tfenv` reads automatically), or a shared internal wiki/README listing the approved version per project.

    5. **Enforce it in CI/CD** — the pipeline should install/use the exact pinned version (via `tfenv` or a Docker image with a fixed Terraform version baked in) so CI is the ultimate gatekeeper — even if a developer's local version drifts, CI catches it before merge/apply.

    6. **Standardize via a shared Docker image** — many teams build an internal "Terraform runner" Docker image with the approved CLI + provider versions + tools (`tflint`, `checkov`) baked in, and require all `plan`/`apply` (local and CI) to run inside that container.

    7. **Upgrade process** — when bumping versions across 15 projects, do it in a controlled batch: test the new version against one low-risk project first, review the `terraform plan` output for unexpected diffs (provider upgrades can introduce them), then roll out incrementally rather than all at once.

    8. **Communicate changes** — version bumps go through a PR and changelog entry so all 4 developers know when and why the standard changed.

---

### 📝 Tips Before Your Interview
- Be ready to explain concepts **with a real example**, not just definitions.
- Practice explaining **state file mechanics** — it's one of the most commonly probed areas.
- Be comfortable discussing **trade-offs** (e.g., workspaces vs. separate state files) rather than giving one "correct" answer.
- Know the difference between **what Terraform does automatically** vs. **what requires manual intervention** (like state file loss, drift, or renames).
