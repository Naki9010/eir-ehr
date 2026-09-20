# Tables (Chart Renderer)

Primary source: [docs/PLUGINS.md](../PLUGINS.md) ("Replace The Chart").

`apps/web/renderers/table.js` is one of two bundled chart renderers
(alongside `timeline.js`), both exporting `render(target, records)` and
receiving the same authorized, immutable JSON record snapshots — see
[../architecture/frontend.md](../architecture/frontend.md) for the full
contract. `renderers/shared.js` holds logic common to both (e.g. formatting
helpers).

The table renderer is one of the two configured in every shipped profile
(`chartRenderers: ["timeline", "table"]`) and can be selected as the
`defaultRenderer`; both are exercised by the same browser test suite at
desktop and mobile widths (see [../testing/e2e-tests.md](../testing/e2e-tests.md)).

There is no third bundled renderer today. Adding one is documented as a
supported extension point, not a currently-shipped feature — see
[../architecture/frontend.md](../architecture/frontend.md) "The chart-
renderer contract".
