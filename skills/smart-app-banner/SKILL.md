---
name: smart-app-banner
description: Add, fix or check an Apple Smart App Banner (the `apple-itunes-app` meta tag) so a website promotes its iOS app in Safari on iPhone and iPad. Use when the user asks for a Smart App Banner, an "open in app" / "get the app" banner for iOS Safari, or the banner does not show. Not for handling the URL inside the iOS app, Android install banners, or custom JavaScript banners.
---

# Smart App Banner

Make Safari on iOS/iPadOS show Apple's native app banner on the pages the user
chooses, with the correct App Store ID and, when the app supports it, a URL that
opens the matching screen in the app.

Source: Apple, "Promoting Apps with Smart App Banners"
(https://developer.apple.com/documentation/webkit/promoting-apps-with-smart-app-banners).

## What the banner does

- A thin strip at the top of the Safari page with a Close button. After the user
  closes it, it does not reappear when they return to the page.
- App installed: tapping the banner opens the app.
- App not installed: tapping opens the App Store page. Back on the website, a
  progress bar shows the download; when it finishes, the button becomes Open.
- If the device cannot run the app, or the app is not sold in the user's
  region, the banner does not appear at all.

## Workflow

1. **Get the `app-id`.** It is the numeric Apple ID of the app:
   - App Store Connect → the app → General > App Information → Apple ID, or
   - the App Store link `https://apps.apple.com/<region>/app/<name>/id<NUMBER>`.
   Never guess or reuse an ID from another project. If the user has not given
   it and the repo does not contain it, ask for it.
2. **Find where `<head>` is rendered** in the project (check `package.json` and
   the layout/head files) and decide which pages get the banner. Put the tag on
   those pages only, inside `<head>`, once per page.
3. **Decide on `app-argument`.** Add it only when the iOS app already handles that
   URL (universal link or custom URL handling). If that is unknown, leave it out
   and tell the user what it would enable. Handling the URL inside the app is
   outside this skill.
4. **Add the tag** using the matching snippet below. Render it in the HTML the
   server sends (static or SSR); do not rely on injecting it with client-side
   JavaScript after load.
5. **Verify** (see the last section).

## Syntax

```html
<meta name="apple-itunes-app" content="app-id=123456789, app-argument=https://example.com/products/42">
```

| Parameter | Required | Value |
|---|---|---|
| `app-id` | Yes | Numeric Apple ID of the app. |
| `app-argument` | No | A valid URL passed to the app when the user opens it from the banner. |

Parameters are comma-separated inside `content`. Useful `app-argument` values:

- Deep navigation: the page's full URL, so the app opens the same item.
- Search: a URL carrying the query, so the user continues the search in the app.
- Content creation: a URL carrying a session ID, so the app can resume the work.

Generate `app-argument` per page on the server. Percent-encode commas in the URL
(`%2C`) because a comma separates parameters, and HTML-escape `&` when writing
raw HTML.

## Snippets

Plain HTML:

```html
<head>
  <meta name="apple-itunes-app" content="app-id=123456789">
</head>
```

Next.js App Router (static, e.g. in `app/layout.tsx`):

```ts
import type { Metadata } from "next";

export const metadata: Metadata = {
  itunes: { appId: "123456789" },
};
```

Next.js App Router, per-page `app-argument`:

```ts
import type { Metadata } from "next";

export async function generateMetadata(
  { params }: { params: Promise<{ id: string }> },
): Promise<Metadata> {
  const { id } = await params;
  return {
    itunes: {
      appId: "123456789",
      appArgument: `https://example.com/products/${encodeURIComponent(id)}`,
    },
  };
}
```

Next.js Pages Router: render the plain `<meta>` inside `next/head`.

Nuxt:

```ts
useHead({
  meta: [{ name: "apple-itunes-app", content: "app-id=123456789" }],
});
```

Astro or any server template: write the `<meta>` in the page `<head>`, building
`app-argument` from the request URL (`Astro.url.href` in Astro).

Keep the App Store ID in one place (a constant or public env var) when several
pages or layouts use it.

## Why it does not show

- Not Safari on iOS/iPadOS: other iOS browsers and desktop browsers ignore the tag.
- The page is inside a frame or iframe.
- Testing in the iOS Simulator: the banner never appears there. Use a real device.
- The user already closed the banner for this site.
- The app is not available in the user's region or not supported on the device.
- The tag is missing from the server HTML, outside `<head>`, duplicated, or has a
  non-numeric `app-id`.

## Verify

1. Check the HTML the server returns:

   ```bash
   curl -s https://example.com/page | grep -io '<meta name="apple-itunes-app"[^>]*>'
   ```

   Expect exactly one match on each banner page, inside `<head>`, with a numeric
   `app-id` and, if present, a valid `app-argument` URL. Check that pages which
   should not show the banner return no match.
2. Open the deployed page in Safari on a real iPhone or iPad. Report this step as
   pending if no device test was possible; a passing `curl` check alone does not
   prove the banner displays.
