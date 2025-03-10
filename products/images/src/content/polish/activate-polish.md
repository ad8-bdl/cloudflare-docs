---
pcx-content-type: how-to
---

# Activate Polish

Images in the [cache must be purged](https://developers.cloudflare.com/cache/how-to/purge-cache) or expired before seeing any changes in Polish settings.

<Aside type="warning" header="Warning">

Do not activate Polish and Image Resizing simultaneously. Image Resizing already applies lossy compression, which makes Polish redundant.

</Aside>

1. Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/) and select the account and domain where you want to activate Polish.
1. Click **Speed** > **Optimization**.
1. Under **Polish**, select *Lossy* or *Lossless* from the drop-down menu.
1. (Optional) Click **WebP**. Enable this option if you want to further optimize PNG and JPEG images stored in the origin server, and serve them as WebP files to browsers that support this format.

<Aside type="note">

To ensure WebP is not served from cache to a browser without WebP support, disable WebP at your origin web server when using Polish.

</Aside>