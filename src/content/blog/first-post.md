---
title: 'My First Site Audit: A Canonical Pointing at a Stranger'
description: 'What a first Ahrefs audit of a two-day-old Astro site found: a canonical tag pointing at someone else, redirecting links and template leftovers.'
pubDate: 'Oct 16 2026'
---

This site is two days old, and its first technical audit already found a problem that could have kept every page out of Google. Here is what Ahrefs Site Audit reported, what each issue actually meant, and what changed after the fixes.

## The starting point

The site runs on the Astro blog template and is deployed to a free Cloudflare Workers subdomain. The first crawl gave a Health Score of 92%, with 4 internal URLs flagged with errors. That sounds fine. The details were not.

## Issue 1: the canonical pointed at someone else's site

Every page carried a canonical tag pointing at `seo-lab.pages.dev`. I had guessed that address when setting up the project, but the site was deployed to `seo-lab.shinrv2006.workers.dev`. When I opened the guessed address, it turned out to be an unrelated pet-care blog.

A canonical tag tells Google which URL is the original version of a page. Mine said that the original lived on a stranger's domain. Ahrefs surfaced it as "Canonical URL has no incoming internal links", which is a quiet way of describing a serious mistake.

The fix was one line: the `site` value in `astro.config.mjs`, which Astro uses to build canonical URLs and the sitemap. The `Sitemap` line in `robots.txt` had the same wrong host and needed the same fix.

**Lesson:** set the site URL only after the first deploy, when the real address is known.

## Issue 2: internal links went through redirects

The navigation linked to `/blog` and `/about`, while the server answered with a 301 to `/blog/` and `/about/`. Every page linked through a redirect. I updated the links and added `trailingSlash: 'always'` to the Astro config, so the build and the dev server follow one rule.

## Issue 3: template leftovers

The title "Astro Blog", the description "Welcome to my website!", a "Lorem ipsum" about page, social links to Astro's own accounts and five demo posts. None of this is broken, but all of it tells a crawler that nobody owns the site. I wrote real titles and descriptions, added a missing H1 to the blog index, and deleted the demo posts.

## Two things that looked like problems but were not

**A Domain Rating of 91.** Ahrefs calculates DR for the root domain, and the root domain here is `workers.dev`, which belongs to Cloudflare. My own site has zero referring domains. Any site on a shared subdomain, whether `vercel.app`, `pages.dev` or `blogspot.com`, shows its host's DR.

**A 404 on the homepage.** The URL was `www.seo-lab.shinrv2006.workers.dev`, a host that does not exist. No page linked to it; Ahrefs was testing the www version on its own. The fix was in the crawl settings, not in the site.

## Results

The Health Score went from 92% to 96%, and the errors on real pages went to zero. After the demo posts were removed, the score moved to 93%: with fewer pages, one remaining crawl-settings issue weighs more. On a small site the score swings with a single URL, which is why I track the issues themselves rather than the number.

## Next

The same checks on a client-side rendered React site, where the bigger question is whether a crawler sees any content at all without JavaScript.