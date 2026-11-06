# Trance for Claude

Trance runs a brand's marketing from a Claude chat: Radar (where to grow), strategy, calendar, ideas, posts, carousels and videos, blog
articles, email, SEO and GEO audits, Meta Ads, analytics and market research. Its agents work on the Trance server and your own Claude is
the model that thinks for them, so you talk to Claude and Trance does the work and publishes it.

This plugin gives Claude the skill and the commands to work with Trance, and it connects Claude to the Trance connector.

## What you need

- A Claude account on a paid plan.
- A Trance login: the connector asks you to sign up or log in the first time.
- **Radar is free**: with a Trance login and no subscription you can connect your accounts, set your brand up and run Radar. Everything else
  (strategy, calendar, posts, blog, email, audits, Meta Ads) needs a Trance subscription, which Claude offers you when Radar is ready.

## Install

Add this plugin from Claude (Settings → Plugins), or from Claude Code:

```
/plugin marketplace add reelisticapp/trance-plugin
/plugin install trance@reelistic-trance
```

The connector is part of the plugin (`.mcp.json`): `https://trance-api.reelisticapp.com/trance/mcp`. If you have already added the Trance
connector by hand, Claude shows its tools once.

Then say: **"Set up Trance for my brand"**.

## Commands

| Command | What it does |
| --- | --- |
| `/trance-setup` | Takes you through the setup: your brand, your accounts, Radar, strategy |
| `/home` | Where things stand and what is next |
| `/guide` | What Trance can do and what to say to do it |
| `/radar` | Radar: where to grow, and how the brand performs against where it started |
| `/ideas` | Ideas for the next post |
| `/plan-week` | Fill the coming week of the calendar |
| `/posts` | Posts waiting for your approval |
| `/calendar`, `/schedule` | Your calendar and your posting schedule |
| `/strategy` | See or change the content strategy |
| `/analytics` | How the brand is doing |
| `/research` | Market research: competitors, trends and news |
| `/brand`, `/connections` | Your brand's files and your connections |
| `/trance-daily` | The scheduled run: does the work that is due |

## What this plugin does and sends

The plugin is text only: one skill, 15 commands and one agent that tell Claude how to work with Trance. It has no hooks and no scripts, and it
installs and runs no code on your computer. Its one connection is the Trance connector declared in `.mcp.json`
(`https://trance-api.reelisticapp.com/trance/mcp`, over HTTPS, with your own Trance login). What you ask Trance to do, and the brand
information you give it, is sent to that server, which stores your brand, strategy, calendar, posts and results so Trance can work on them.
Nothing is sent anywhere else by the plugin.

## Your data and your keys

- You keep control of what is spent: images and videos are made with your own WaveSpeed key, voice with your own ElevenLabs key, and Meta Ads
  campaigns are built paused and only you switch them on.
- Keys and passwords are typed in a panel inside the chat, never in the conversation.
- You can delete your account and all your data from the chat at any time.

More: https://trancegrowth.com · Privacy: https://trancegrowth.com/privacy
