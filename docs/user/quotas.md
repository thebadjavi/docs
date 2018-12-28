<h1>Quotas</h1>

The quotas can limit the resources used by each role, category, group or user individually.

[TOC]

# Types of quotas

There are a set of quotas that can be modified:

- **Desktops**: Maximum number of desktops that can be created.
- **Running:**: Maximum number of desktops that the user can have running at the same time.
- **Templates**: Maximum number of templates that the user can create from their desktops.
- **Media** (or Isos): Maximum number of media (isos/floppies) that the user can upload to the system.
- **CPUs**: Maximum number of virtual CPUs that can be set to a desktop.
- **Memory**: Maximum memory in MB that can be set to a desktop.

# Effective quotas for user

A user is always being classified within a role, category and group, in that order of hierarchical priority. This means that the effective quotas for a user will be the most restrictive from top to down.

If we do modify quota for a user individually, then that quota will override the one that had before based on role, category and group hierarchy.