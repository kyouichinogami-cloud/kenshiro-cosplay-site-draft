# GA4 setup readiness — HIDDEN ARMOR site

Status as of 2026-11-25: GA4 is NOT installed. No Measurement ID has been supplied or verified. Do not insert a placeholder ID into production HTML.

Site:
https://kyouichinogami-cloud.github.io/kenshiro-cosplay-site-draft/

## When the owner provides a real Measurement ID

1. In Google Analytics, create or select the GA4 property.
2. Add a Web data stream for the exact site URL above.
3. Copy the Measurement ID from Web stream details. It must be the actual ID issued by Google (normally begins with G-).
4. Confirm the ID against the GA4 property and stream before implementation.
5. Add Google's official Google tag snippet to the shared <head> of all seven HTML pages. Replace the ID placeholder only with the verified ID.
6. Deploy and verify collection in GA4 Realtime / DebugView before reporting that measurement works.

Official setup guide:
https://support.google.com/analytics/answer/9304153?hl=en

Official Measurement ID guide:
https://support.google.com/analytics/answer/9539598?hl=en

## Measurement plan

Recommended initial events:
- page_view (GA4 enhanced measurement / page load)
- click_youtube_channel
- click_youtube_subscribe
- click_video
- click_playlist
- click_materials
- click_gallery

Use descriptive event parameters only where actually implemented, such as link_url and link_text. Do not collect names, email addresses, or other personal information.

## Reporting guardrails

- Do not report traffic, clicks, conversions, or audience metrics until GA4 has collected them.
- Distinguish GA4 website traffic from YouTube Studio watch time and YPP eligibility.
- A website click to YouTube does not prove a YouTube view or a monetized watch hour.
- Keep the Measurement ID out of public source-control documentation if the owner prefers; it is not a password, but avoid publishing any secret credentials or API keys.
- Never use a fabricated Measurement ID.


## Verified page inventory and YouTube click event

The repository tree was checked on 2026-11-25. It contains these seven HTML pages:
- index.html
- videos.html
- gallery.html
- materials.html
- process.html
- armor-process.html
- faq.html

A previous six-page checklist omitted armor-process.html. The implementation scope is seven pages, not six.

After the real Measurement ID is verified, add the Google tag once to the head of all seven pages. Add the following event listener once before </body> on each page, after the Google tag is available. Do not deploy this code before the real ID is verified.

```html
<script>
(function () {
  if (typeof window.gtag !== 'function') return;
  function placement(link) {
    if (link.dataset.gaPlacement) return link.dataset.gaPlacement;
    if (link.closest('nav')) return 'site_navigation';
    if (link.closest('footer')) return 'site_footer';
    if (link.closest('.cta')) return 'call_to_action';
    return 'main_content';
  }
  document.addEventListener('click', function (event) {
    const link = event.target.closest('a[href]');
    if (!link) return;
    let url;
    try { url = new URL(link.href, window.location.href); } catch (_) { return; }
    const host = url.hostname.toLowerCase();
    if (!(host === 'youtube.com' || host.endsWith('.youtube.com') || host === 'youtu.be')) return;
    window.gtag('event', 'outbound_youtube_click', {
      page_location: window.location.href,
      link_url: url.href,
      link_text: (link.innerText || link.getAttribute('aria-label') || '').trim(),
      placement: placement(link)
    });
  }, true);
})();
</script>
```

Required event parameters: page_location, link_url, link_text, placement.
The event name is outbound_youtube_click. Do not treat clicks as YouTube views or monetized watch time.
