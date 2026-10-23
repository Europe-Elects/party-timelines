# Party timelines

Builds one timeline page per country from the party sheet and publishes them on GitHub Pages.
A scheduled GitHub Action downloads the sheet every hour, regenerates the pages and deploys them.

## Files

- `scripts/build.py` – reads the sheet (TSV) and writes one HTML page per country into `site/`
- `scripts/build_index.py` – writes `site/index.html`, the country index
- `scripts/template.html` – the page template (styling, colour-by toggle, tooltips)
- `scripts/load.py` – reads the TSV and refuses to build if expected columns are missing
- `data/latest.tsv` – the last downloaded copy of the sheet (each change is a commit, so the history is kept)
- `.github/workflows/build.yml` – the hourly build and deploy

## Build locally

```
python scripts/build.py data/latest.tsv site
python scripts/build_index.py site
```

Open `site/index.html` in a browser. No packages are needed beyond Python 3.

## Rebuild now

Actions tab → "Build party timelines" → Run workflow.

## Embed on a website

```html
<iframe id="party-timelines" src="https://YOUR-USER.github.io/party-timelines/"
        style="width:100%;border:0;height:1400px" loading="lazy"
        title="European party timelines"></iframe>
<script>
  window.addEventListener("message", function (e) {
    if (e.origin !== "https://YOUR-USER.github.io") return;
    if (e.data && e.data.type === "party-timeline-height") {
      document.getElementById("party-timelines").style.height = e.data.height + "px";
    }
  });
</script>
```

Point `src` at a country page (for example `.../germany.html`) to embed one country.
The script makes the frame grow to fit the page; without it the frame keeps the fixed height and scrolls.

## If a build fails

The live site keeps the last good version. The Actions log names the problem, usually a renamed
or deleted column in the sheet's header row, or a sheet link that is no longer published.
