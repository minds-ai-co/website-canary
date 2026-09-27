# Website canary pages

Test pages for Minds' website-evidence canary (`minds-ai-co/webapp`, workflow
`website-evidence-canary.yml`). Every few hours the canary asks Minds on
staging about each site in [`sites.json`](sites.json). It then checks that the
question shows its website, that every answer was generated with the
screenshot, and that the answers mention something only visible on the page.

The pages are invented, so their facts never go stale. Each one reproduces a
pattern that broke website capture in the past (webapp #6557):

| Page | Pattern |
|------|---------|
| [`ticket-shop/`](ticket-shop/) | Nothing renders until a consent choice, every choice reloads the page, and the content is a dense seat map |
| [`event/`](event/) | An ordinary page with a non-blocking cookie notice (the baseline) |
| [`news/`](news/) | The site's own consent banner, then a vendor dialog in an iframe, and a settings link that navigates away |

`sites.json` is the canary's site list: it holds these pages plus live sites
that only get the screenshot checks. Rules for facts:

- A fact is visible **only after** the page's consent walls are cleared. It is
  never in the page `<title>`, in the URL, or on a page a wrong click leads to,
  so answers from a capture that stopped at a wall cannot match it.
- Every wall covers the whole page, so a stuck capture shows no content.
- Patterns are JavaScript regular expressions matched against each answer, in
  both German and English wording where the answer might translate.

To add a pattern, add a page and a `sites.json` entry in the same PR. To check
a change before it merges, run the canary with the `sites_url` input pointing at
the raw file on your branch.

The pages carry `noindex` and are not linked from anywhere.
