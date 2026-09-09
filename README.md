## Title

Regression(Select): floating content components lost `max-h-(--bits-*-content-available-height)` in #2471

## Describe the bug

`Select.Content` has `overflow-y-auto` but no `max-height`, so it grows to the full height of its item list instead of being capped to the available viewport space. With a long list the popover runs past the bottom of the window.

Nothing scrolls it back. `scrollHeight === clientHeight` on the content, so `overflow-y-auto` never engages and the viewport inside it is never compressed, which also means `SelectScrollUpButton` and `SelectScrollDownButton` can never appear. Items below the fold are unreachable by wheel and by keyboard, and since the content is `position: fixed` the page cannot scroll to them either.

bits-ui publishes the value needed to bound it, on that same element. It is never read:

```
--bits-select-content-available-height: 764px;
```

`max-width` is `none` for the same reason, so long item labels also push the content wider than the window.

**Bisect.** `select-content.svelte`:

| revision | date | `max-h` present |
| --- | --- | --- |
| `0a17af2b` | 2025-06-08 | yes |
| `7ff65b48` | 2025-12-15 | yes |
| `24b3ae4a` (#2471) | 2026-03-18 | no |
| `main` | 2026-09-09 | no |

`dropdown-menu-content.svelte` lost `max-h-(--bits-dropdown-menu-content-available-height)` in the same commit. As of today no floating content component in the `nova` style declares a `max-h`: select, dropdown-menu, popover, context-menu, menubar.

**Expected.** Content capped to the available viewport height and scrolling internally, as it did before #2471.

I intend to submit a PR: restore `max-h-(--bits-<name>-content-available-height)` on the content class of each of those five components. Happy to scope it to `select` alone if you prefer a narrower change.

## Reproduction

<!-- REPLACE with your StackBlitz or GitHub repo URL. A snippet alone gets the issue closed. -->

Open the select. Measured on `[data-slot="select-content"]` at viewport 1280x800:

| | |
| --- | --- |
| rendered size | 1062 x 5600 px |
| computed `max-height` / `max-width` | `none` / `none` |
| `scrollHeight` / `clientHeight` | 5600 / 5600 |
| wheel scroll 3000px | no movement |
| 40x ArrowDown | highlighted item at y=1520, off screen |

## Logs

No console or server output. The failure is purely layout.

## System Info

```
  System:
    OS: macOS 26.3.1
    CPU: (8) arm64 Apple M3
    Memory: 164.61 MB / 16.00 GB
    Shell: 5.9 - /bin/zsh
  Binaries:
    Node: 25.6.1 - /opt/homebrew/bin/node
    npm: 11.9.0 - /opt/homebrew/bin/npm
    pnpm: 10.29.2 - /opt/homebrew/bin/pnpm
    bun: 1.3.9 - /Users/imlargo/.bun/bin/bun
  Browsers:
    Chrome: 152.0.7977.83
    Safari: 26.3.1
  npmPackages:
    @lucide/svelte: ^1.43.0 => 1.43.0
    @sveltejs/kit: ^2.63.0 => 2.70.3
    bits-ui: ^2.19.2 => 2.19.2
    shadcn-svelte: ^1.6.1 => 1.6.1
    svelte: ^5.56.1 => 5.57.0
    tailwindcss: ^4.3.0 => 4.3.3
```
