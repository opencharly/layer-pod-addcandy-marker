# pod-addcandy-marker

A single-file **marker** layer for OpenCharly images — the `add_candy`-on-pod
overlay witness.

The `pod-addcandy-marker` candy exists solely to be `add_candy`'d onto a pod
deploy whose deploy key differs from its image name. It drops two files into the
overlay image:

| File | Token | Leg exercised |
|---|---|---|
| `/etc/pod-addcandy-marker` | `POD-ADDCANDY-MARKER-OK` | `write:` (inline `COPY`, no scratch stage) |
| `/etc/pod-addcandy-copied` | `POD-ADDCANDY-COPIED-OK` | `copy:` (against the per-candy `FROM scratch` context stage) |

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `pod-addcandy-marker` |
| Files | `/etc/pod-addcandy-marker`, `/etc/pod-addcandy-copied` |
| Bundled asset | `copied.dat` (the `copy:` source) |
| Packages | none — pure file layer, overlays on any base |
| Service / port | none |

## Why it exists

It is the witness the `check-addcandy-pod` bed uses to prove that the
`<deploy-key>-overlay` image (which carries the layer) is the one actually
deployed — and not the base image. If `charly config` resolved the base image
instead, the marker tokens are absent and the bed fails; if the `copy:` source
was not staged into the overlay build context, the overlay build errors at
`COPY .build/_candy/…`.

## How to use it

Attach it to a deploy via `add_candy:`:

```yaml
my-pod:
  pod:
    add_candy:
      - github.com/opencharly/layer-pod-addcandy-marker
```

The candy's `plan:` asserts both marker files exist and carry their distinctive
tokens inside the running container.

## Layout

- `charly.yml` — the `pod-addcandy-marker:` candy entity (the `write:` and
  `copy:` run steps plus the `file:` `check:` probes).
- `copied.dat` — the bundled `copy:` source.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning family skill: `/charly-core:deploy` (`add_candy:` overlay)
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
