# wri-incident-panos

Tile pyramids for **disposable** incident and drill 360 panoramas, served by GitHub Pages
and opened by `panos/pano.html` (Pannellum, multires).

Split out of [`wri-panos`](https://github.com/Whitewaterrescue/wri-panos) in September 2026:
that repo holds the **permanent** GRP corridor spheres and was approaching the 1 GB
GitHub Pages published-site ceiling. Incident spheres have the opposite lifetime — they
are disposable once the incident or drill is over — so they live here where they can be
pruned hard, or the repo recreated, without risking the GRP reference panos.

## Layout

```
panos/pano.html            the viewer  (?tiles=tiles/<id>&title=...)
panos/tiles/<pano_id>/     one tile pyramid per sphere: config.json, 1/ 2/ 3/ 4/, fallback/
```

`<pano_id>` is the **GlobalID of the queue row that produced it**, braces stripped and
lowercased. It is assigned by AGOL, so it is globally unique, needs no filesystem listing
to compute, and can never be reused after a prune — unlike the `wri-panos` `NNNN` sequence,
where deleting the highest ids made the next run hand them out again and silently
repointed a live `pano_url` at a different sphere.

Source JPEGs are deliberately **not** kept here: the tiler writes a low-res `fallback/`
cube, which is all the viewer needs before tiles stream in, and the originals are
20-55 MB each.

## What writes to it

`Initial Response Apps/pano-tiles/` — the GitHub Actions worker behind the WRI Field App's
**360 Panoramas** page. A pilot picks an equirectangular JPEG, it uploads as chunked
attachments on the `WRI_Pano_Requests` queue layer, and the worker tiles it, pushes here,
and appends a point to the shared **WRI Incident 360 Panoramas** layer that already sits
on every client stable map.

## Pruning

A sphere is two things: a directory here and a feature on that AGOL layer. Remove both in
the same pass or a live map dot opens a 404. The Field App page's **Remove** action does
both; do not delete directories by hand.
