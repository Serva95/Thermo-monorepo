# Thermo-monorepo (umbrella checkout)

A **single working tree** for the three components of the thermo home-heating
system. This repository contains **no component code** — it is an *umbrella*:
it pins the three component repositories, as git submodules, to the named
commits below. One clone gives you the whole system at pinned, consistent
versions.

(The name `Thermo-monorepo` is historical. No code of the components was
ever merged here and no component history was rewritten; the three
repositories stay independent in remote, branch model, history, and CI.)

## Components

| Path | Repository | Pinned branch | Role |
|---|---|---|---|
| `ThermoSmartSpring/` | [Serva95/ThermoSmartSpring](https://github.com/Serva95/ThermoSmartSpring) | `opencode` | Control web app: business logic, routine scheduling, state persistence. **Conventions home of the whole system.** |
| `ThermoTools/` | [Serva95/ThermoTools](https://github.com/Serva95/ThermoTools) | `opencode` | Headless decision backend: per-room on/off algorithm, relay action. Single canonical tree: `2.7.18-J/` (Java). |
| `ThermoWrapperK/` | [Serva95/ThermoWrapperK](https://github.com/Serva95/ThermoWrapperK) | `master` | Always-on supervisor: version check, download, relaunch, Let's Encrypt certificate lifecycle. |

All three run on the same host and share the same `thermo` database.

## Getting started

```sh
git clone --recurse-submodules https://github.com/Serva95/Thermo-monorepo.git
cd Thermo-monorepo
```

or, for an existing clone:

```sh
git submodule update --init
```

Build each component from its own directory (see `AGENTS.md` for the
per-component entry points and environment notes).

## Bumping a pin

```sh
git -C ThermoSmartSpring fetch && git -C ThermoSmartSpring checkout opencode && git -C ThermoSmartSpring pull
git add ThermoSmartSpring && git commit -m "build: bump ThermoSmartSpring pin to <short-sha>" && git push
```

Each pin commit must point at a commit already pushed to the component's own
remote — never at unpushed local work.

## Conventions home

The system's cross-component decision record is **one** catalogue:
`ThermoSmartSpring/adr/` (ADRs 0001–0005) plus `ThermoSmartSpring/INDEX.md`,
with its `CONVENTIONS.md` and `plan/` queue beside it. That catalogue is the
authoritative record for decisions that touch more than one component.

This umbrella root adds **no** second `CONVENTIONS.md`, `adr/`, or `plan/`.

Component repositories that already carry their own internal catalogue
(notably `ThermoWrapperK/adr/`) keep it — those records are that component's
own and remain component-level.
