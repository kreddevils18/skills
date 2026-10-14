# smart-app-banner

Makes Safari on iPhone and iPad show Apple's native Smart App Banner on the pages you choose: the right App Store ID in an `apple-itunes-app` meta tag and, when your app handles it, an `app-argument` URL that opens the same screen in the app.

## Install

```sh
npx skills add https://github.com/kreddevils18/skills --skill smart-app-banner
```

Or paste this prompt into any agent. It loads the skill for the task without installing it:

```text
Run `npx skills use "https://github.com/kreddevils18/skills" --skill "smart-app-banner"` and follow the generated skill instructions now. Read its complete output, redirecting it to a temporary file first if necessary. Resolve relative paths from the supporting-files directory it provides.
```

Manual install for Claude Code:

```sh
git clone https://github.com/kreddevils18/skills
cp -R skills/skills/smart-app-banner ~/.claude/skills/
```

## Use

The skill loads when you ask for a Smart App Banner, an "open in app" or "get the app" banner for iOS Safari, or when the banner does not show. You can also call it by name: `/smart-app-banner` in Claude Code, `$smart-app-banner` in Codex.

| You say | What the agent does |
| --- | --- |
| "Add a Smart App Banner for our iOS app" | Asks for the numeric App Store ID if the repo does not have it, finds where `<head>` is rendered, adds the tag to the pages you pick |
| "Open the same product in the app from the banner" | Adds a per-page `app-argument` built on the server, only if the app already handles that URL |
| "The app banner does not show on my site" | Checks the server HTML, the tag syntax and the usual causes: not iOS Safari, iframe, Simulator, dismissed banner, region |

## What it covers

- The `apple-itunes-app` syntax: `app-id`, `app-argument`, comma and `&` escaping.
- Snippets for plain HTML, Next.js App Router (`metadata.itunes`), Next.js Pages Router, Nuxt and Astro.
- A `curl` check of the server HTML and a real-device check in Safari.

Not covered: handling the URL inside the iOS app, Android install banners, custom JavaScript banners.

## Security audit

Passed 7 of 7 checks on 2026-10-14 (`ak:skill-creator` audit): no scripts, no secrets, no network calls, no dependencies. See [`audits/smart-app-banner.json`](../../audits/smart-app-banner.json).

Source: Apple, [Promoting Apps with Smart App Banners](https://developer.apple.com/documentation/webkit/promoting-apps-with-smart-app-banners).
