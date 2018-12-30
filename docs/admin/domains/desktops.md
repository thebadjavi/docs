<h1>Administer Desktops</h1>

[TOC]

# Desktop actions

There are some actions that administrator can perform on each desktop, besides of start and stopping it:

- Template: Lets the admin convert any desktop in the system to a template shared to others.
- Edit: It is the generic desktop edit that also has the desktop user owner but now with the limits and permissions of the administrator.
- Delete: Will delete user desktop. Not recoverable.
- XML: You can view raw libvirt XML and modify it. This is practical whe trying new functionalities.
- Messages: It will show the raw bunch of engine JSON messages associated with this desktop.
- Events: It iwll show the raw bunch of engine JSON events associated with this desktop. 

# Global actions

There is a dropdown with a list of actions that can be performed on a set of desktops. Desktops can be selected clicking over them. Not selecting any desktop will lead to a whole desktop set selection, be carefull:

- **Toggle status**: This action will toggle *start* and *stopped* status. All the desktops selected will switch their status at once.
- **Stop no viewers**: All the desktops started in the selection set that the system detected that no one is connected with a viewer will be stopped.
- **Force stopped**: Whatever state has the desktop a *stopped* state will be set.
- **Force failed**: Whatever state has the desktop a *failed* state will be set.
- **Delete**: It will set all the selected desktop status to *deleting* if it is in stopped or failed status. That will initiate in background engine all the actions to reach complete deletion of desktops. If anything fails in that process the desktop will remain in a Failed state. You can always trigger delete action again on *deleting* desktops and they will be deleted from database ignoring the background engine.

# Create new desktop

