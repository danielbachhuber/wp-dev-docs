Checking Capabilities
=====================

When a user performs an action which alters the database, let it be submitting a settings form, modifying a user’s profile details, or editing post data, we want to check that they have the *capability* to do so. WordPress makes this easier to do through its system of [Roles and Capabilities](https://codex.wordpress.org/Roles_and_Capabilities).

Before we go to much further, let's review. A *role* is a collection of capabilities. WordPress creates roles like Administrator, Editor, and Author by default — they correlate very much with which permissions you'd want to assign in a publishing workflow. And a *capability* is pretty much that: a defined permission. In its default roles, WordPress uses capabilities like `manage_options`, `edit_posts`, and `switch_themes`. When a WordPress user is created, they're assigned a role, and the collection of capabilies associated with the role defines what they can do.
