---
description: Get your Trance workspace, every panel in one page beside the chat
---

Use the trance skill. Call `trance_workspace` (with `language`, the language the user writes in). Publish its `page` as a **new** artifact exactly as given, following its `how`: declare the artifact's `mcp` capability for the Trance connector with the tools `trance_app_read`, `trance_app_do` and `trance_app_media`, or the page cannot reach Trance and only says it works inside Claude. Never reopen an artifact of theirs from before: one published without that access stays broken. If the `mcp` capability is not offered to you, do not publish it: tell them in one line that the workspace needs Claude with artifacts that can use connectors, and that everything in it is also in the chat. Then ask the user once, in their language, whether to pin it in their sidebar, and pin it on a yes (or tell them to pin it from the artifact's menu if you cannot pin).
