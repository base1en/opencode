# Example Verbose Extended Context Log

## Context Tracking System
**SESSION START**
- Read /homeassistant/agent-context.yaml to load previous session context. The file contains a timestamped history with session summaries.
- If `/homeassistant/agent-context.yaml` does not exist, create it with the following contents: history: []
  
**WRITING SUMMARY RULES**

*By default, ask the user before writing. An explicit user request to write overrides this confirmation step.*

"Would you like to save a summary? (y/n)"

- Write only after confirmation.
- If the user declines, do not write and do not ask again for the same task within the current session.
- If the user explicitly requests a write, skip the confirmation prompt and write immediately.
- Do not create duplicate entries within the previous 1 hour window, based on timestamps, unless significant new information or a meaningful state change has occurred.
- A user-requested write always overrides the 1 hour deduplication window.

*Subject to the rules, write when any of the following occur:*

- The user explicitly requests it.
- The user exits a session using a prompt — treat this as an explicit user-requested write.
- A discrete task or problem has been fully resolved.
- A significant decision has been made.

**WRITING OPERATIONS**
 - Append a summary of the current session to the history list in /homeassistant/agent-context.yaml
 - Always use homeassistant_render_template with {{ now() }} to get the current local time before writing
 - Include: key actions taken, decisions made, and any important context for future sessions
 - Use the format: session: "YYYY-MM-DD HH:MM - Brief description" and summary: "..." (including actions taken, decisions made and any important context within the summary)

**EXAMPLE YAML FRAGMENT WRITTEN TO FILE:**

    - session: "2026-07-25 17:06 - TC71 privacy mode toggle fix"
      summary: >
       Fixed inverted privacy mode toggle on TC71 camera dashboard. The dashboard
       was using switch.tc71_cam_1 (main camera on/off) labeled as "Privacy Mode",
       so toggle ON=camera active, OFF=privacy felt backwards to the user. Decision:
       created an inverted template switch rather than relabeling, to match Tapo Android app
       labeling and behavior. Created packages/tc71.yaml with switch.tc71_privacy_mode — ON means
       privacy engaged (camera off), OFF means camera active. Icon dynamically shows
       eye/eye-off. Updated both Camera Settings and Camera Status dashboard cards
       to use the new entity. Important context: the real entity behind the toggle is
       still switch.tc71_cam_1; template inverts it. Entity appears after template
       reload or HA restart.
