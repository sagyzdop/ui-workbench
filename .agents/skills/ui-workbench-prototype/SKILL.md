---
name: ui-workbench-prototype
description: Prototype a component or a route screen in the UI workbench, then write it for real. Use when the user wants to try a design, explore a layout, or build something before committing to it.
---

A prototype is **throwaway code that answers a question**. The question decides
where it goes: `components/` for a component, `route-screens/` for a route screen, a whole
screen rendered the way its route will render it. The workbench renders both beside the
project's tokens, so the answer is something the user can look at rather than something
you describe.

When exploring the codebase, read `GLOSSARY.md` (if it exists) so names match the
project's domain language, and respect ADRs in the area you are touching. The
folder convention is documented in the workbench's README.

## References

`ui-workbench/references/` holds what the user wants looked at: a screenshot, a
snippet, a spec somebody sent them. Nothing in it is rendered and nothing in it
is imported.

The folder is passed to you, not scanned. Read a reference when the user names
one, points at one, or attaches one to the request. Do not go through the folder
looking for something that looks relevant, and do not read its contents to work
out what to build.

When a reference is in play:

- A screenshot is the spec. Where it and the code disagree, the screenshot is what
  they want and the code is what you are fixing.
- Say which reference the prototype answers, so the user can tell that you read it.
- Never import or mount anything from `references/`. It is not part of the app.
- Build with shadcn and the codebase's own conventions whatever the reference looks
  like. A reference drawn with a different library, or another project's components,
  is a shape to match rather than a dependency to add.
- If nothing there covers the area, say so and build from the codebase's conventions.

## Process

### 1. State the question

Write down what you are trying to find out in one sentence. If it cannot be
answered by looking at something, it is not a prototype, it is a build.

### 2. Start the workbench

`pnpm workbench`. One command, and the user should have it running before you
write anything. The port is whatever that command prints, so read it from the
output rather than assuming 5173.

### 3. Put it in the right folder

A **component** goes in `components/` as `name.wb.tsx`, rendered under its filename so it is
identifiable in a long list. A **route screen** goes in `route-screens/` as `name.wb.tsx`,
rendered full-bleed with no heading so it can be judged at the viewport it will actually run
at.

Name a route screen after the URL path it serves. Both folders are globbed flat, so the path
is flattened into the filename: `/account/settings` is `account-settings.wb.tsx`,
`/p/courses` is `p-courses.wb.tsx`. The sidebar shows the filename, so the path is what the
user reads.

Take the path from the router's file tree rather than inventing it. A `_`-prefixed
directory is a pathless group and is not part of the URL; every other segment is. Where a
segment is dynamic, such as `$slug`, it has no name to take: substitute a plausible one,
write one line at the top of the file saying which route it stands in for, and tell the user
you assumed it. If no reference or existing route suggests what the screen is, say what you
picked and why before building it.

A route screen cannot import from the app's own routes. Those bring a shell and a
session fetch the workbench has no business making. Build its layout from shared
components.

A route screen fills the frame, which is one viewport less the workbench's header,
not the window. Say `min-h-0` or `h-full`, not `min-h-svh`, which is one header
taller than the space it gets.

### 4. Build variants, only when the question is open

Three files, three sidebar entries, nothing to wire up. Do this when the answer
is a shape or a direction, such as which layout an empty state takes. Do not do
it for a component that already has a spec: write one file and iterate on it.
The workbench's value is a fast loop on one thing.

Keep fixtures in a sibling `.wb.demo.tsx`. That suffix is skipped by the sidebar,
so the component stays readable when the data behind it is large, and it is what
the token walk picks up, so a token that only appears in a fixture still shows
under the component. A plain `.demo.tsx` is skipped by both, and its tokens go
missing without anything looking wrong.

### 5. Confirm it renders

Look at the page before calling it done. A hook called outside its provider, or
any render that throws, leaves a blank page that types, tests, lint and build
all pass, and "the page is empty" says nothing about why.

```sh
node scripts/browser.mjs http://localhost:5173/ 1200 800 out.png
```

It starts headless Chrome itself and exits non-zero if the page logged an error
or threw. Vite's overlay covers a throw in the code you just wrote; this catches
the ones that leave the page blank with nothing to read.

### 6. Write it for real

Stop when the question is answered. Say so, then write the component into the
app, against its real props, and delete the fixture. Do not move the workbench
file: it has fixtures baked in and hardcoded props, so it is not the file you
want.

Your prototype is a new file. Anything already in `components/` came with the
workbench, so leave it alone unless the user asks.

## Anti-patterns

- **Don't polish.** No tests, no loading states, no error handling, no
  abstractions. Vite's overlay is the error reporting.
- **Don't generalise.** The workbench answers one question.
- **Don't build the component twice.** Iterating on a file is the loop. If you
  catch yourself re-deriving a decision you already made in the workbench, you
  have lost the prototype.
- **Don't commit your prototype.** The workbench app and its own files are
  tracked; nothing under the workbench folder is gitignored, so a scratch file
  shows up in `git status` on its own. Leave it there.
