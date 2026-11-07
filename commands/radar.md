---
description: Radar - where the business should grow
---

Use the trance skill. Show the latest Radar with `trance_show_radar`, passing `language` (the language the user writes in); with no Radar yet, run it with `trance_radar` first. A free account with no brand set up starts with the express Radar: `trance_ask(preset="radar_start")` asks only for their website, then `trance_radar(website=...)`. A question in $ARGUMENTS is answered from `trance_radar` (pass it as `question`); never run a new scan unless they ask for one.
