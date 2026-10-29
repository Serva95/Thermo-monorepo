# AGENTS — umbrella checkout

This directory is an **umbrella checkout** (ADR 0005): one working tree
holding the three components of the thermo heating system as git
submodules, each pinned to a named commit. There is no component code in
this repository.

## Read this first

* **Conventions home:** `ThermoSmartSpring/`
  `ThermoSmartSpring/adr/` (ADRs 0001–0005) + `INDEX.md` is the
  system's cross-component ADR catalogue; `CONVENTIONS.md` the shared
  authoring rules; `plan/` the work queue. Read these before making
  cross-component changes.
* **Component-internal catalogues are the component's own:**
  `ThermoWrapperK/adr/` (and its `CONVENTIONS.md`, `plan/`) records
  wrapper-local decisions and stay component-level.
* **Never** add a second `CONVENTIONS.md`, `adr/`, or `plan/` at this
  umbrella root.

## Components and their build entry points

| Path | Branch | Build | Notes |
|---|---|---|---|
| `ThermoSmartSpring/` | `opencode` | `./gradlew build` | web app; MariaDB/Postgres |
| `ThermoTools/` | `opencode` | `cd 2.7.18-J && ./gradlew build` | **no** top-level `gradlew` — it lives in `2.7.18-J/`; the `2.7.18-J/` Java tree is canonical |
| `ThermoWrapperK/` | `master` | `./gradlew build` | `contextLoads()` needs a reachable Postgres at its `spring.datasource.url` |

## Rules of the road

1. **Work happens in the component, commit happens in the component.**
   The umbrella only ever commits gitlink (pin) bumps plus its own
   `README.md` / `AGENTS.md` / `.gitignore` / `.gitmodules`.
2. **Preserve local work.** `ThermoWrapperK/src/main/resources/application.properties`
   is a live configuration file and must never be staged or committed.
   If touching a component repository with dirty state, commit by pathspec
   (`git commit -- <path>`), not `git add -A`.
3. **Pins point at pushed commits only.** A submodule pin must reference a
   commit reachable on the component's `origin` — never an unpushed local
   commit — or a fresh `git clone --recurse-submodules` on another machine
   will fail to check it out.
4. **ADR privacy:** no ADR numbers or titles in user-emitted application
   strings, logs, or API responses (READMEs in these private builder
   repositories are internal documentation and may name them).
5. **Signing:** commits are signed (EDDSA per the account configuration);
   `commit.gpgsign` is on globally — do not bypass it with
   `commit.gpgsign=false`.
