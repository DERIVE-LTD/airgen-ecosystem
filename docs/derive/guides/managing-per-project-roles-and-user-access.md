# Managing per-project roles and user access

Derive's permission model is **per-project**: a user can be a viewer
on one project, a contributor on another, and an admin on a third.
This is intentional — most projects have different audiences, and
a tenant-wide model gives too many people too much access.

This guide walks through the role hierarchy, the admin workflows for
inviting users and setting roles, and the conventions that keep the
permission map sane on a multi-project tenant.

> **Prerequisites:** admin role on at least one project (to set
> roles), or admin role on the tenant (to invite new users
> entirely). Most users will have neither — they'll be invited by
> someone who does.

## The role hierarchy

Roles are project-scoped. Derive's roles, in increasing privilege:

| Role         | Can read | Can comment | Can author | Can baseline | Can configure | Loop control |
| ------------ | -------- | ----------- | ---------- | ------------ | ------------- | ------------ |
| **viewer**   | ✓        | —           | —          | —            | —             | —            |
| **commenter**| ✓        | ✓           | —          | —            | —             | —            |
| **author**   | ✓        | ✓           | ✓          | —            | —             | —            |
| **maintainer** | ✓      | ✓           | ✓          | ✓            | partial       | —            |
| **admin**    | ✓        | ✓           | ✓          | ✓            | ✓             | ✓            |

(Exact role names may vary by Derive version — check the **Admin →
Roles** page for the canonical list. The shape is consistent.)

**Loop control** — pause, unpause, submit directives — is restricted
to admins by default. This is intentional: directing the autonomous
loop is a high-impact action.

## The admin pages

Two admin surfaces:

- **Tenant admin (`/admin`)** — only for tenant admins. Manages
  users at the tenant level (invite, deactivate), tenant settings,
  analytics.
- **Project admin (`/p/<slug>/admin` or via the project header)** —
  for project admins. Manages roles per project, project settings.

## 1. Invite a new user (tenant admin)

From `/admin/users`:

1. **Add user** — enter email, optional display name.
2. The user gets an invite email with a one-time signup link.
3. They sign in via GitHub or Google OAuth (linked to their email).
4. Once signed in, they appear in the user list with no project
   roles assigned yet.

By default, new users see no projects. The next step is granting
roles.

## 2. Grant project roles (project admin)

From `/p/<slug>/admin/users`:

1. **Add user to project** — search by email, select the role.
2. The user can now see the project at their assigned level.

To change a role later, find the user card on the project admin
page and pick a new role from the dropdown.

To remove a user from a project, set their role to **none** (or use
the remove button if your version exposes it explicitly).

## 3. Bulk-grant roles

For onboarding many users at once (e.g. a new programme starting):

```sh
# Pseudocode — exact CLI subcommands may vary by version
airgen project users add acme widget-x \
    --email alice@example.com --role author
airgen project users add acme widget-x \
    --email bob@example.com --role viewer
```

Or from the Derive admin UI's "Bulk add" button if your version has
it. Either way, scripts are preferable — clear audit trail, easy to
re-run.

## Card-per-user UI

The user-management UI on Derive renders a card per user, with:

- Name, email, OAuth provider.
- Their role on this project (dropdown to change).
- Their roles on other projects (read-only summary).
- A search filter at the top.
- Collapsible sections (collapse all / expand all) for managing
  large user lists.

This format scales better than a table when you have 50+ users —
the search filter and collapsible cards keep the page manageable.

## Per-project conventions that work

A few patterns that keep multi-project tenants workable:

- **One admin per project, plus a backup.** Two admins per project
  is enough; more dilutes accountability.
- **Authors get write, viewers get read.** Don't promote everyone
  to admin "just in case" — admin role enables loop control and
  project-config changes.
- **Use commenter for stakeholders.** Customers, regulators, and
  reviewers usually want to leave comments on requirements without
  being able to author or baseline.
- **Audit role changes.** Derive logs role changes to the activity
  feed; check it after onboarding rounds to confirm no one ended
  up with surprise admin access.

## Single sign-on patterns

OAuth (Google or GitHub) is the supported login. For corporate
deployments where you want SSO via the company IdP:

- **GitHub Enterprise.** The GitHub OAuth flow works against
  Enterprise installations; configure the client ID / secret in
  Derive's env.
- **Google Workspace.** Similarly, configure Google OAuth with the
  workspace's client ID. Optional `hd=` parameter restricts logins
  to your domain.

For SAML or OIDC integrations beyond Google / GitHub, contact
[Derive Ltd](https://derive-ltd.co.uk) for managed-deployment
options.

## Removing access

To remove a user from the entire tenant:

- **Tenant admin** → user list → deactivate the user. They lose
  access to every project; their authored content remains
  attributed to them.

If they should retain authorship credit but shouldn't have access
going forward, deactivate. If they should be **deleted entirely**
(rare; usually only for spam accounts or legal requirements), use
the delete action instead. Deletion is irreversible and reassigns
their authored content to a "deleted user" placeholder.

## What's next

- [Reading the per-project dashboard](./reading-the-per-project-dashboard.md)
- [Publishing a public read-only demo project](./publishing-a-public-read-only-demo-project.md)
- [Driving the autonomous loop](./driving-the-autonomous-loop.md)
