# Content-Driven UI

For content-heavy pages, keep the page component as the composition layer. Extract repeated presentational units into colocated `.tsx` components; keep data orchestration in the page.

When navigation, tabs, filters, or anchor links mirror structured content, prefer deriving those UI items from the content model instead of duplicating IDs or labels in components. Add explicit display fields such as `navLabel` when the UI needs shorter copy than the full content title.

For client components, pass small typed props derived by the server or composition layer rather than importing broad content objects into the client boundary.

When a refactor changes the content contract, update the relevant validator or narrow verification check in the same change.
