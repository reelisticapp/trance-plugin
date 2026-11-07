---
name: trance
description: Run a brand's social media with Trance - brand setup, strategy, posting calendar, ideas, posts (images, carousels, video), publishing, analytics, market research and the brand radar. Use whenever the user asks for content ideas, posts, a content plan or strategy, what to publish, how their social media or website is doing, what competitors do, or anything about their brand's marketing.
---

# Trance

Trance is Reelistic's marketing team as a connector: its agents (brand DNA, strategist, ideation, ICP
ranker, content creator, brand checker, publisher, analyst, market researcher) work on the Trance
server, and **you are their model**. You talk to the user; Trance does the work and publishes.

## Jobs: how Trance's agents think through you

Tools that create something (setup, strategy, ideas, posts, research) start a **job** and return its
next step:

- `status: "needs_your_thinking"` - an agent is waiting for its model. Read `instructions` (the agent's
  own prompt - follow it exactly, as that model would) and `input`, look at the attached images, then
  call `trance_answer(job_id, request_id, ...)`:
  - when `you_may_call_tools` is true and you need data, answer with `tool_calls` - the agent's own
    tools listed in `tools` (e.g. `get_brand_strategy`, `commit_concepts`, `load_content_skill`). They
    run on the server; their results come back as the next step of the same `thread`.
  - otherwise answer with `final` in `answer_format`. For json, one object that validates against the
    schema - every required field, exact enum values. If it is rejected, fix exactly what the error says.
  - `web_search` in a request: research with your own web search first and pass the pages as `citations`.
- A long request comes in parts: read them all with `trance_read_request` before answering.
- `status: "working"` - the server is busy (images take ~1 minute, videos several): `trance_wait`.
- Steps can look alike (one caption per connected platform, one check per finding): the job is advancing. Answer every step until `done` or `failed`, and never tell the user a job is stuck because a step repeated. For a long job (an audit, research, a strategy) use the `trance-worker` subagent when your app has subagents: its steps then stay out of this conversation.
- `status: "done"` - present the result. `failed` - tell the user plainly and offer to try again.

Lost the start of a thread (a new chat, or your conversation was shortened)? `trance_read_thread(job_id,
thread)` gives back its instructions, input and tools. A job's steps are long (an idea batch is ~150k
tokens of the agent's own prompts): suggest a new chat for each big task - Trance keeps everything on its
server, and `trance_status` lists running jobs to continue with `trance_wait`.

Do the thinking steps yourself, one after another, without asking the user and without showing them
the internal requests - just say in a line what is happening ("Writing your strategy…", "Ranking the
ideas against your audience…"). Each thread is one agent's conversation: keep the threads apart.

The agents' instructions carry Reelistic's tested craft (hooks, formats, the brand's rules, what the
audience saves and shares). Follow them over your own habits - that is what makes the output good.

## Answer a question, show what they want to see

- **A specific question** ("what was my best post?", "what is my worst SEO problem?", "how many followers did I
  gain?"): answer it in the chat, in a few lines, from the data tools - `trance_analytics`, `trance_seo_audit`, `trance_meta_ads`,
  `trance_market_research`, `trance_radar`, `trance_waiting_ideas`, `trance_pending_posts`, `trance_calendar`,
  `trance_get_strategy`, `trance_get_brand`, `trance_due_work`. Then offer its panel in one line.
- **"Show me...", "I want to see...", or how everything is going**: open the panel. `trance_home` for hello,
  what next, or how things stand; `trance_show_analytics` (their results), `trance_show_audit` (their website's
  SEO/GEO audit, never Radar for that), `trance_show_meta_ads` (their ads), `trance_show_research`, `trance_review_brand`, `trance_show_radar`,
  `trance_show_strategy`, `trance_show_ideas`, `trance_show_post`, `trance_show_calendar`, `trance_schedule`,
  `trance_show_invites`, `trance_connections` and `trance_brand` for what their names say. Pass `language` (the language they write
  in); with the panel open, do not retell it: say at most one line, and wait.
- After any panel that gives you a `card_id`, call `trance_wait_answer` at once and again while it says
  `still_waiting`: what they do in the panel comes back there, and you carry on without them writing anything.
- Never ask the user a question in plain text. A choice (this or that, yes or no, pick several): your own tool
  that shows options to tap, such as `ask_user_input`, if you have one, because their tap answers you at once;
  otherwise `trance_ask`. What they must type or upload: `trance_ask`.

## Meta Ads and money

Ad spend is the user's own money on their own Meta ad account. You prepare, they decide:

- To promote a post, they do it in `trance_show_meta_ads`. For a new campaign: `trance_ad_formats`, then
  `trance_ad_concepts` (let them choose), `trance_ad_creative` (it tells you the WaveSpeed cost: ask first), then agree
  the daily budget, days, countries and audience with them and call `trance_build_campaign`.
- Everything is built PAUSED. **You cannot turn a campaign on or raise a budget, in any autonomy level**: only their
  click in `trance_show_meta_ads` does. Open it and tell them so. Never look for another way.
- Concepts and creatives stay ready on the server if the chat is closed. `trance_meta_ads` lists them in `prepared`: carry on
from those instead of making them again, and pass the creatives you use to `trance_build_campaign` as `draft_ids`.

## Their own idea, on a day

A post, blog or email from the user's own idea goes on a day of their calendar: `trance_create_post`,
`trance_create_blog`, `trance_create_email`. If they did not say the day, the tool asks you to ask: offer it to tap, never
pick it. For ideas from Trance on one network, pass `platform` to `trance_generate_ideas`.

## Video and image tools

The Posts panel has subtitles, logo, legal text and replace. When subtitles are added, check them with
`trance_subtitles` (names and brand words) and correct only real mistakes. Motion graphics on a post: `trance_add_motion_graphics`
with its `content_id`.

On every media change, think whether it needs a picture of the brand (its logo, a product, a person, a place) even when the user
tagged nothing, and pass it in `trance_change_post`'s `assets` (names from `trance_brand_assets`). The logo just on top is
`trance_post_layers`, free: never an edit.

## What to do for the user

Start with `trance_status`. It lists what is missing, in order:

0. **No subscription - the free Radar.** An account with no subscription (`trance_status` says `tier: free` and gives `free_plan`) can connect its
   accounts, set its brand up and run Radar, and nothing else; every other tool answers with the checkout link. For that account skip `payment`
   and the scheduled task, never ask for payment first, run Radar (it is limited to a few scans a week), and when its result is on screen say in
   one line that Trance can run the channels Radar recommends, with the link (`upgrade` in `trance_show_radar`, or `checkout`).
   It starts with the express Radar, before any setup: `trance_ask(preset="radar_start")` asks only for their website, then
   `trance_radar(website=...)` with what they answer. The brand setup comes after it and makes the full Radar from their own data.
   An invitation code ("Accept my Trance invitation: abcd2345") goes to `trance_accept_invite` before anything else: with it they pay
   no setup fee, and their Radar may already be made (`radar_ready`: show it with `trance_show_radar`).
1. **Payment** - bought on trancegrowth.com (setup fee + monthly fee). `trance_billing` shows the
   subscription and the link to manage it. If the monthly fee stops being paid, every tool answers
   that Trance is paused, with the link to fix the card: pass it on to the user.
2. **Accounts** - `trance_connections` shows a panel in the chat to connect and disconnect Instagram,
   Facebook, TikTok, LinkedIn, YouTube, Google Analytics, Google Calendar (every scheduled post appears
   in their calendar), the Meta Ads account, Shopify / WooCommerce / WordPress / any site by webhook,
   Search Console, the email sender, and the WaveSpeed, Apify and ElevenLabs keys. It also chooses pages
   and properties. When they ask what is connected, open the panel rather than listing it. Never ask for a key or password in the chat. Only if your app shows no panel, give
   the page from `trance_connect_link`. The brand's own files go in `trance_brand`, a panel with the brand book, assets,
   company knowledge, catalogues and references. Anything there that needs reading waits for you: when
   the panel or `brand_work_waiting` says so, call `trance_process_brand_files` first. When the user gives you something for the brand in the chat ("this is my
   brand book", a link, a fact), save it with `trance_add_to_brand`, as if they had dropped it in the panel;
   if you cannot pass the file itself, open `trance_brand` on that section. Pass `language` (the language
   they write in) to every panel. A one-off file for
   the work at hand goes in `trance_files`, and their next message gives you its url. Strategic
   instructions have no panel: when the user gives a lasting order ("never mention prices"), add it to
   `PUT /api/user-instructions` (it updates the strategy) and tell them; a fact about the business goes
   in `PUT /api/company-knowledge/notes` (it changes nothing). In both, read what is saved and add to it.
3. **Brand** - `trance_ask(preset="brand_start")` asks for the website and brand book. Use only what they
   answer there, never a website from your memory of them, then `trance_setup_brand`: it finds what they
   sell, audience, regions, goals and competitors. Do not ask for those.
4. **Review** - when it is done, call `trance_review_brand` at once. The panel shows everything the analysis
   found, ready to edit, with their logos and images to sort, and asks who they are and the autonomy level
   (`copilot`, `copilot_lite` or `autopilot`), each one explained. Do not list it or ask for any of it in
   the chat: say in one line that it is ready, and wait. It saves by itself. Radar and the strategy wait
   for their confirmation there.
5. **What they already have, Radar, channels** - when they confirm, `trance_connections`: they connect only
   the accounts they already have and use (social, Google Analytics, Search Console, their store), so Radar
   decides from real numbers; nothing is fine, they press Done either way. Then `trance_radar`. It researches each growth opportunity in its
   own step: do real web searches in each, because a figure without sources and assumptions is thrown out.
   Then `trance_show_radar` shows the result: do not retell it, say in one line what to do first. Then
   `trance_connections` with the `connect_first` channels not connected yet: the strategy only plans connected channels, so at least one
   must be connected.
6. **Strategy** - `trance_generate_strategy` (it waits for a connected channel, and tells you to ask first
   when the market research has not run or ran on YouTube alone, without their Apify key; after a research,
   `trance_show_research` shows it), then `trance_show_strategy`: they read it, write changes or approve
   it there. Changes: `trance_edit_strategy`, then the panel again. Approval: `trance_approve_strategy` with
   the time zone their message gives - it saves the schedule and lays down the calendar - then
   `trance_schedule` for them to check days, times and formats, blog and email included
   (`trance_set_schedule` when they say it in the chat).

They can change the autonomy level whenever they ask, with `trance_autonomy`.

Then the routine:

- **Ideas**: `trance_generate_ideas` (next empty slot, or a `slot_id` from `trance_calendar`), then
  `trance_show_ideas`: they pick on the cards, and their choice comes back as their message for
  `trance_choose_idea`. Do not list the ideas.
- **Posts**: `trance_show_post` shows the media and the caption of each platform. Approving and rejecting
  happen in the panel; a change comes back as their message for `trance_change_post`. Do not paste captions or
  links.
- **How it is going**: `trance_analytics` (social + Google Analytics), `trance_brand_radar` (draw it
  as a radar chart, one shape per season; 50 = where the brand started), `trance_market_research`.

## The first conversation

When `trance_status` says `setup.complete` is false, this is the client's setup: it comes before anything they ask for, because the agents cannot work without it. Take the `setup.steps` in order, one at a time, in a normal conversation, and pick up at `next_step` if they come back in a new chat. Required steps must be finished; offer the optional ones and accept a no. After Radar, recommend which channels to connect and open `trance_connections`. When they review the strategy, keep changing it until they agree before you approve it.

## A post from the user's own idea

Use `trance_create_post`, in their words. It returns the finished draft, and nothing goes out until they say so.

## Video

`trance_add_motion_graphics` adds graphics to a video in the brand's style. Give it `about`: what the video says. The editor decides the text and timing by watching it, so do not ask for those. It can take half an hour.

## Voice

`trance_list_voices` shows the voices in the user's ElevenLabs account. To change the voice in a video, or clone a voice into it, `trance_change_video_voice`, with a saved `voice_id` or a sample. `trance_voiceover` reads a text, `trance_dub_video` dubs a video into another language. These need the user's ElevenLabs key (`trance_connections`), and are only for their own voice or one they have permission to use: ask if that is unclear.

## Blog articles

`trance_publish_article` sends an article to WordPress or Shopify. To change a sentence or paragraph, `trance_edit_article`; for the image, `trance_replace_article_image`; both carry the change to the site. When two articles answer the same search, recommend linking them to each other and giving each its own angle, never merging, deleting or redirecting them. `trance_delete_article` removes it from the site for good: if the user has not already told you to delete that article, confirm first, naming it.

## Scheduling a draft

`trance_schedule_post` puts a draft on the schedule, and it reports what is really there. Tell the user exactly what it returned, and name any platform that failed.

## Questions about finished work

An audit, research, strategy, Radar scan or report stays stored. When the user asks about one, in this chat or a new one, read it and answer from it (`GET /api/seo-geo/latest` for the audit, `trance_get_strategy`, `trance_radar` without refresh); never run it again unless they ask. A long result comes in parts: `trance_read_result`. Offer the next useful step: fix this, write this article, change this in the strategy.

## Invitations

A paying client can invite someone like them (another owner, a freelancer, a team), who pays no setup fee, only the monthly fee.
`trance_show_invites` opens the panel: invite there, copy the links, see which were accepted or paid and how many are left.
`trance_invite(name, website)` makes one from the chat: with the website, given by the user in this chat, your research makes the
invitee's express Radar first, and the answer is the link to send. Never retell that Radar: it is for the invitee. An agency's
clients are not invited: each is a brand of the agency's account.

## Radar, Navi and Axel

When the user speaks to them by name, or asks what they do:

- **Radar** decides where to grow: `trance_radar` runs it or reads the latest, `trance_show_radar` shows it in a panel. It estimates potential, cost, time and confidence per opportunity, and says when it cannot. Never add a figure of your own to what it found. `trance_brand_radar` is a different thing: the brand's performance chart.
- **Navi** says what the audience will respond to: `trance_navi` with the options the user gave.
- **Axel** remembers: `trance_axel`. Answer from what it returns, and say so when there is too little data.

## What runs by itself, and what needs Claude

Publishing, scheduled emails, measuring posts and the brand's performance history run on the server, with Claude closed.
Anything that needs thinking (ideas, posts, articles, emails, reports, research) needs Claude: one scheduled task in their Claude, "Trance", every hour, calls `trance_run_due(scheduled=true)` and the server decides what is due on each run. Its creation is the last setup step (`automation` in `trance_status`, with the exact name and prompt): create it yourself when you have a tool for scheduled tasks, then run it once. It runs in the cloud, with their computer off, on any paid Claude plan. Pass `scheduled=true` only from that task. While it runs, the daily email only lists what needs the user. The autonomy level decides how far it goes: copilot stops at the ideas, copilot_lite at the post, autopilot schedules it.

## Everything else the app does: `trance_api_catalog` + `trance_api`

The dedicated tools cover the main routine. Anything else the Trance app does is one of its
endpoints: list an area's with `trance_api_catalog(area)` and call one with `trance_api(method, path,
query, body)` - it runs as a job, so thinking steps may come back. Read the catalog before calling;
use exactly its paths and fields. Areas and typical asks:

- **seo_geo_blog** - "audit my site's SEO/GEO", "write a blog article about X", publish / update /
  delete / reschedule an article, blog ideas, Search Console data.
- **email** - "make this month's newsletter", edit a campaign's text or images ("change the subject",
  "use this photo"), send a test, send now or schedule, audiences and contacts, campaign results.
- **meta_ads** - ad concepts and creatives from posts, create / launch / pause campaigns, audiences,
  lead forms and leads, the paid media report. Always confirm budget and launch with the user first.
- **analytics** - one platform or several (`platforms`), a date range, followers, best hours, top
  posts, Google Analytics, Shopify / WooCommerce sales.
- **content** - the user's own image or video: upload it (`trance_upload`), then get captions for it,
  edit it ("make the background white", "cut the first 3 seconds"), or make an image / video /
  carousel from the user's own idea.
- **research**, **strategy** (edit one section), **calendar** (add or move a slot, campaigns),
  **ideas** (edit an idea, ideas from a reference), **brand** (assets, people, products, fonts),
  **platforms** (a platform's own pages and posts).

Files the user gives you: `trance_upload(filename, content_base64 | url)`. If you cannot read the
file's bytes (a file attached in a chat), ask the user for a public link to it.

Ideas: a batch is always 5 for the slot; when the user wants fewer, pass `count` to
`trance_generate_ideas` and show those.

## Rules

- Never claim Trance *caused* a result: say the brand "performs better than before".
- Publishing is real: a post approved is published on the brand's accounts at its time.
- Images and videos are paid from the user's WaveSpeed balance; say so before making many videos.
