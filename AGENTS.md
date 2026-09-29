# AGENTS.md — layer-pod-addcandy-marker

Standalone candy repo for the `pod-addcandy-marker` layer — a single-file marker
fixture authored solely to be `add_candy`'d onto a pod deploy. The candy lives in
`charly.yml` at the repo root: the `write:` and `copy:` run steps, the `file:`
`check:` probes, and the bundled `copied.dat` copy source. It carries **no
`skill:` entity**.

Canonical files:

- `charly.yml` — the `pod-addcandy-marker:` candy entity (the `write:` + `copy:`
  steps and the `file:` `check:` probes; no `skill:` entity).
- `copied.dat` — the bundled `copy:` source.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-core:deploy` — the family skill: the `add_candy:` overlay, the
  pod-deploy lifecycle, and the overlay Containerfile synthesis this fixture
  witnesses. Load before editing or troubleshooting the candy.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `write:`/`copy:`/`check:`). Load before editing any
  entity field or plan step.
- **Missing owning skill:** this fixture carries no `skill:` entity, so no
  repo-owned skill is projected for the candy; the closest family skill is
  `/charly-core:deploy` (the `add_candy:` overlay). The gap is recorded against
  the named batch
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- `charly box validate` at the repo root checks the manifest parses and
  validates.
- The candy's own proof is its `plan:` — the `write:` + `copy:` steps and the
  `file:` `check:` probes — exercised inside the `check-addcandy-pod` bed, which
  asserts both tokens are present in the running container.

## Modify this repo

- Keep the two marker paths and their tokens stable: the `check-addcandy-pod`
  bed asserts both, so a change here is a cross-repo change.
- The `copy:` source must stay inside this candy directory; a `..` traversal is
  rejected at validate time.
- If an owning skill is authored, add the `skill:` entity here and update this
  signpost and the README in the same change.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
