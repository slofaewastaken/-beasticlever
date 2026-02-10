# BeastiClever

## Opening your game in `about:blank` from Google Sites

If your site is hosted on Google Sites (`sites.google.com/view/beasticlever`), there is an important limitation:

- Google Sites does **not** let you inject custom JavaScript into normal text/link widgets.
- A regular hyperlink can open a new tab, but it cannot dynamically write HTML into `about:blank` by itself.

So the practical approach is:

1. Host a small launcher page somewhere you control (GitHub Pages, Netlify, Vercel, etc.).
2. Put a normal link on Google Sites that points to that launcher page.
3. In the launcher page, run JS that opens `about:blank` and injects your game iframe.

### Launcher page example

Create `launcher.html` with this:

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Launch Game</title>
  </head>
  <body>
    <button id="launch">Open Game</button>

    <script>
      const gameUrl = "https://YOUR-GAME-URL.example";

      document.getElementById("launch").onclick = () => {
        const w = window.open("about:blank", "_blank");
        if (!w) {
          alert("Popup blocked. Allow popups for this site.");
          return;
        }

        w.document.write(`
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8" />
              <title>Game</title>
              <style>
                html, body { margin: 0; height: 100%; background: #000; }
                iframe { border: 0; width: 100%; height: 100%; }
              </style>
            </head>
            <body>
              <iframe src="${gameUrl}" allowfullscreen></iframe>
            </body>
          </html>
        `);
        w.document.close();
      };
    </script>
  </body>
</html>
```

Then add a Google Sites link to your hosted `launcher.html`.

### Notes

- This only works if your game URL allows embedding in an iframe (no restrictive `X-Frame-Options` / CSP `frame-ancestors`).
- Some browsers block popups unless triggered by a direct user click.
- You can skip `about:blank` entirely and just link directly to the game URL in a new tab if that is acceptable.
