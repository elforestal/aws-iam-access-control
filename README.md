<img width="735" height="590" alt="image" src="https://github.com/user-attachments/assets/6090b9d0-0694-42df-a3fb-da7d2d52f482" />

## Introducing Today's Project!

### Project overview

In this project, I will use AWS IAM to enforce least-privilege access control. I'll launch two EC2 instances — one tagged as production, one as development. Then I'll create an IAM policy that lets a user manage only development resources, not production. To prove it works, that user will successfully stop the development instance but get denied when trying to stop the production one. This is attribute-based access control (using tags as the condition) combined with least privilege — a core part of secure cloud architecture. It also pairs with my AWS KMS project, where access was controlled at the encryption-key layer instead of the IAM layer.

### Tools and concepts

**Services used**
- 🔐 **AWS IAM** — created policies, users, and groups
- 💻 **Amazon EC2** — the instances I controlled access to
- 🧪 **IAM Policy Simulator** — validated permissions without touching live resources

**Key concepts**
- Least-privilege access control
- IAM policy structure — `Effect`, `Action`, `Resource`, and `Condition`
- Tag-based (attribute-based) access control — scoping access by an `Env` tag
- IAM users and groups — attaching policies at the group level for scalable management
- Account aliases for simpler sign-in
- **Two-way access validation** — tested live (as the intern user) and via the Policy Simulator, confirming production was denied and development allowed exactly as designed

### Project reflection

About 1 hour.

---

## Tags

### What I did in this step

In this step, I will launch two Amazon EC2 instances to add computing power for NextWork. I'll tag one instance as production and the other as development. These tags are the foundation of the whole project. Later, an IAM policy will use them to control which resources a user is allowed to manage. This is a real-world pattern — tagging resources by environment so access and permissions can be scoped to them. It sets up the least-privilege access control I'll build and test in the next steps.

### Understanding tags

Tags are labels you attach to AWS resources, written as a key-value pair — for example, Env: production or Env: development. They're useful in several ways. They make resources easy to find and filter, so you can pull up everything in one environment at once. They help with cost allocation, letting you track spending by project, team, or environment. And — most importantly for this project — they can drive access control. An IAM policy can grant or deny permissions based on a tag, so a user might be allowed to manage development resources but blocked from production. That last use is what makes tags a security tool, not just an organizational one.

### My tag configuration

I assigned each instance a Name tag and an Env tag. The first instance has Name: nextwork-prod-forestal with Env: production. The second has Name: nextwork-dev-forestal with Env: development. The Env tag is the key one for this project. It's what the IAM policy will use to allow access to development resources while denying access to production.

![Image](http://nextwork.ai/passionate_rose_serene_emblica/uploads/aws-security-iam_2e0e5a5d)

---

## IAM Policies

### What I did in this step

In this step, I will create an IAM policy that grants access to the development EC2 instance only. The policy is for onboarding an intern who needs to work in the development environment. It uses a tag-based condition, so it applies to resources tagged Env: development — but not production. This follows the principle of least privilege. The intern gets exactly the access they need to do their job, and nothing more. It prevents accidental changes to production, like shutting down a live instance. This is how IAM scopes permissions to a specific role and environment instead of granting broad access.

### Understanding IAM policies

IAM policies define permissions in AWS — who can do what, to which resources. They're written as JSON documents with a few key parts: an Effect (Allow or Deny), an Action (the operations permitted, like starting or stopping an EC2 instance), a Resource (what the action applies to), and optionally a Condition. The Condition is where this project gets powerful. It can restrict a policy to resources with a specific tag — for example, only instances tagged Env: development. You attach policies to users, groups, or roles to grant them those permissions. This is how AWS enforces least privilege: each identity gets exactly the access defined in its policy, and nothing more.

### The policy I set up

I set up the policy using the JSON editor. Writing the policy directly in JSON gives full control over the structure — the Effect, Action, Resource, and the Condition block that scopes access to resources tagged Env: development. It's also the format you'll see in real-world environments and documentation, so being comfortable reading and editing policy JSON is a practical skill beyond this project.

### Policy effect

The policy's effect is Allow. It grants permission to perform EC2 actions — but only on instances that match the condition, meaning resources tagged Env: development. So the effect is to allow the intern to manage development instances, while giving them no access to production. Even though the effect is "Allow," production stays protected because those instances don't match the tag condition, so the permission never applies to them.

### Understanding Effect, Action, and Resource

Effect, Action, and Resource are the three core parts of an IAM policy statement. Effect is either Allow or Deny — it decides whether the statement grants or blocks permission. Action is the specific operation the policy applies to, like ec2:StartInstances or ec2:StopInstances — the "what can be done." Resource is what the action applies to — the "what it can be done to," such as a specific EC2 instance or, in this project, any instance matching a tag condition. Together they answer: is this allowed or denied (Effect), to do what (Action), on which resource (Resource)? A Condition can be added on top to narrow it further — here, restricting the policy to resources tagged Env: development.

---

## My JSON Policy

![Image](http://nextwork.ai/passionate_rose_serene_emblica/uploads/aws-security-iam_1c864649)

---

## Account Alias

### What I did in this step

In this step, I will create an AWS account alias to simplify the sign-in process. By default, users log in with the 12-digit AWS account ID, which is hard to remember and share. An account alias replaces that number with a custom, readable name in the sign-in URL. This makes it easier for the intern to access the account. It also gives the login link a cleaner, more professional look. It's a small usability improvement that streamlines onboarding for new users.

### Understanding account aliases

An account alias is a custom, human-readable name that replaces the 12-digit AWS account ID in the sign-in URL. Instead of logging in with a string of numbers, users get a clean link like https://nextwork-alias-edith.signin.aws.amazon.com/console. Each AWS account can have only one alias, and it must be globally unique across all of AWS. It makes the sign-in URL easier to remember, share, and look more professional — which simplifies onboarding for new users like the intern.

### Setting up my account alias

About 2 minutes.

![Image](http://nextwork.ai/passionate_rose_serene_emblica/uploads/aws-security-iam_0eb4439b)

---

## IAM Users and User Groups

### What I did in this step

In this step, I will create an IAM user group for interns and an IAM user for the new intern. I'll attach the development-access policy to the group rather than to the individual user. Then I'll add the intern's user to that group, so they inherit the policy's permissions. This means all interns can be managed from one place — update the group's policy once, and every member's access changes with it. It also gives the intern their own login, separate from the admin account. That way they can access development resources without ever touching production, following least privilege.

### Understanding user groups

An IAM user group is a collection of IAM users that share the same permissions. Instead of attaching policies to each user one by one, you attach a policy to the group, and every user in it inherits those permissions. In this project, I created an interns group with the development-access policy attached. Any user added to that group automatically gets development access — and nothing more. This makes permissions far easier to manage. To onboard a new intern, you just add them to the group. To revoke access, you remove them. And updating the policy once updates it for everyone in the group at the same time.

### Attaching policies to user groups

Attaching the policy to the user group means every user in that group automatically inherits its permissions. So the intern, as a member of the interns group, gets the development-access permissions the policy grants — without me having to attach anything directly to their individual user. The effect is centralized permission management. Any user I add to the group gets the same development access, and only that access. If I update the policy later, the change applies to every group member at once. It also keeps permissions consistent across all interns, which reduces the risk of misconfiguring access for any one person.

### Understanding IAM users

An IAM user is an identity in AWS that represents a single person or application, with its own login credentials and permissions. In this project, I created a user for the intern so they have their own way to sign in — separate from the admin account. Each user gets individual credentials, so actions can be traced back to a specific identity. Users get their permissions either directly or, better, by being added to a group that has policies attached. Here, the intern user inherits development access from the interns group. This means they can log in and manage development resources, but never touch production.

---

## Logging in as an IAM User

### Sharing sign-in details

There are two ways to share a new user's sign-in details. The first is to download the credentials as a .csv file, which contains the username, password, and sign-in URL, and share that securely with the user. The second is to email the sign-in instructions directly — AWS can generate pre-written login details you can send to the user. Either way, the user gets their console sign-in URL (with the account alias), their username, and an autogenerated password they can reset on first login.

### Observations from the IAM user dashboard

Logging in as the intern user, I observed that several dashboard panels showed "Access denied" messages. This is expected and correct. The intern's IAM user only has the narrow permissions granted by the development-access policy — permission to manage development EC2 instances, and nothing more. So the console reflects that limited scope. Anything the policy doesn't explicitly allow is denied by default, which is why most panels are inaccessible. This confirms the user is tightly scoped to least privilege from the very first login.

![Image](http://nextwork.ai/passionate_rose_serene_emblica/uploads/aws-security-iam_6f2ab446)

---

## Testing IAM Policies

### What I did in this step

In this step, I will log in as the intern's IAM user to test their access. This is the moment that validates the whole permission setup. I'll try to stop the production instance, which should fail — the intern has no access to production resources. Then I'll try to stop the development instance, which should succeed. If production is denied and development is allowed, the tag-based policy is working exactly as intended. This confirms least privilege in action: the intern can manage only what their role requires, and production stays protected.

### Testing policy actions

As the intern user, I attempted to stop both EC2 instances to test the policy. When I tried to stop the production instance, the action was denied — a red banner told me I wasn't authorized, because the intern's policy doesn't grant access to production-tagged resources. When I tried to stop the development instance, the action succeeded — the instance began shutting down, because the policy allows managing resources tagged Env: development. Same action, two different outcomes, determined entirely by the environment tag on each instance.

### Stopping the production instance

When I tried to stop the production instance as the intern user, the action was denied. A red error banner appeared at the top of the screen telling me I wasn't authorized to perform this operation. This is exactly the intended result. The intern's IAM policy only grants access to resources tagged Env: development, so it has no permission to act on the production-tagged instance. The denial confirms the policy is protecting production from a user who shouldn't be able to touch it.

![Image](http://nextwork.ai/passionate_rose_serene_emblica/uploads/aws-security-iam_0e7a9d6a)

### Stopping the development instance

When I tried to stop the development instance as the intern user, the action succeeded. A green success banner appeared, and the instance moved into the "Stopping" state. This is the intended result. The intern's IAM policy grants access to resources tagged Env: development, so the user is authorized to manage this instance. Paired with the denied production attempt, this confirms the policy is working exactly as designed — access is allowed where it should be, and blocked where it shouldn't.

![Image](http://nextwork.ai/passionate_rose_serene_emblica/uploads/aws-security-iam_1811801c)

---

## IAM Policy Simulator

In this secret mission, I will use the IAM Policy Simulator to test the intern user's permissions. The simulator lets me check what a user is allowed or denied to do — without having to log in as them or perform the actions for real. I'll simulate the intern trying to stop the production and development instances. The simulator should confirm the same result as my live test: denied on production, allowed on development. This is a faster, safer way to validate permissions, because I can verify access before granting it to a real user — and catch any misconfiguration without touching live resources.

### Understanding the IAM Policy Simulator

The IAM Policy Simulator lets you test what a user, group, or role is allowed or denied to do — without performing the actions for real or logging in as that identity. I'd use it to validate permissions before granting access to a live user, so I can confirm a policy is scoped correctly ahead of time. It's also useful for troubleshooting: if someone has unexpected access (or is missing access they should have), the simulator shows exactly which policy is causing that result. This makes it a safe, efficient way to audit and verify access — catching misconfigurations before they reach production, rather than discovering them after something breaks.

### How I used the simulator

For the development instance, the simulation result was allowed. When I set the ec2:resourcetag/env condition to development and simulated the StopInstances action, the Policy Simulator returned "allowed" — confirming the intern user has permission to manage development-tagged resources. This matched the result from my live test, where stopping the development instance succeeded. The simulator validated the policy's behavior without needing to perform the action on a real resource.

![Image](http://nextwork.ai/passionate_rose_serene_emblica/uploads/aws-security-iam_069d8a621)

---

---
