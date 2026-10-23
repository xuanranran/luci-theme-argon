# Argon 2.4.2 baseline comparison and selected backports

## Integration Scope

This fork remains based on the customized 2.4.2 codebase. Its local version
label is updated from `2.5.0` to `3.1.0` at the maintainer's request. Selected
changes through jerrykuku's `v3.1.0` are backported; the version label does not
mean a full upstream replacement or a rewrite of the fork's visual design.
The independent `luci-app-argon-config` package is versioned `3.0.0`.

Preserve curl, SPA navigation, circular progress indicators, existing colors
and fonts, QuickFile behavior and the independent configuration package.
Tests and deployment helpers stay in the ignored local `tests/` directory;
they and the test workflow are not included in the published changes.

## Compared Revisions

- jerrykuku has no `v2.4.2` tag. `5b2e62b` first reports `2.4.2`; `216d9ef`
  is the last commit before the `2.4.3` bump and still reports `2.4.2`.
  These are source snapshots rather than release tags.
- sbwml `openwrt-25.12`: `4b8b784`, reporting `2.4.2-20260210`.
- Local base: `10e0da8`. Its `2.5.0` version is a local relabeling, not
  upstream's 2.5.0 release.
- Backport endpoint: jerrykuku `v3.1.0`, `96734f4`.

Normalize paths when comparing: upstream's package root corresponds to
`luci-theme-argon/` here. The upstream 2.4.2 config app is a separate project;
sbwml keeps the theme and configuration app as two packages in this tree.

## Same-Version Differences

Both upstreams report 2.4.2, but their source snapshots differ:

| Area | jerrykuku 2.4.2 snapshots | sbwml openwrt-25.12 |
| --- | --- | --- |
| Templates | Lua `.htm` | Ucode `.ut` and `ucode-mod-math` |
| Navigation | Traditional page loads | SPA router and menu-tree cache |
| Progress | Original display | Circular indicators and compact spacing |
| Settings | Separately maintained app | Independent JavaScript config package in this tree |
| Styling | Original theme plus pre-2.4.3 modernization | Additional LuCI, plugin, file-browser and dialog fixes |
| Assets | Later Google Sans/WOFF2 changes | Different icon/font selection and WebP background |
| Removal | No matching custom postrm | Restores Bootstrap on removal |

The local fork additionally customizes colors, typography, plugin layouts
and QuickFile scrolling. Already present features such as removal of jQuery,
Ucode templates, ModeMenu and dynamic-list drag indicators are not imported
again.

## Retained Backports

| Area | Retained Changes |
| --- | --- |
| Settings defaults | Validate colors, blur and opacity in both headers; preserve local font and progress-color options |
| Wallpaper sources | Keep curl with TLS verification, HTTP errors and timeouts; add flock dependency, cache fallback, private Unsplash keys and Wallhaven sources |
| Local backgrounds | Support dotted/encoded filenames, final-extension detection, HTTPS source validation and local fallback |
| Authentication | Share the login form, support Authentication Login plugins, escape attributes and retain numeric field limits |
| Login layouts | Optional classic/centered layouts and previews; missing or unsupported settings fall back to classic |
| Branding | PNG logo/favicon upload and reset, content-hash names, bounded image validation, shared head metadata and upgrade preservation |
| Appearance | Persistent light/dark/system shortcut, v3.1.0 button/menu styling and responsive sizing, accessible keyboard controls, failed-save reconciliation and config-widget synchronization |
| Dark colors and glass | Automatic/custom dark accents and inherited/custom login effects; infer legacy choices without writing UCI on load |
| Configuration flow | Native LuCI color widgets and validation events; save before apply; retain hidden custom values |
| RPC permissions | Separate read and write access; reject invalid filenames, JSON, symlinks and upload destinations; retain readonly controls |
| Navigation | Correct nested tabs, sidebar accessibility and reduced-motion behavior while preserving the SPA router |
| Page identity | Resolve first-child entry routes to the rendered page so overview styling and circular-progress initialization work on direct entries |
| Native controls | Dynamic-list sizing, IP/MAC dropdown text flow, empty network selectors and shrinkable file browser |
| Network forms | Scoped firewall/interface action wrapping and responsive summaries without a global table rewrite |
| Repository keys | Current TableSection/PEM layout, wrapped filenames, shrinkable editors and mobile labels; keep original light/dark colors and native key protections |
| Other page fixes | Channel-analysis graph sizing, diagnostics dropdown overflow, MultiWAN cards, firmware-dialog wrapping/contrast, switch/VLAN states and scrollable crontab editing |
| Packaging | Keep Ucode templates as source, disable CSS tidying, retain branding/background data during upgrades and preserve palette-module MIT attribution |

The appearance RPC uses a private UCI delta directory, so changing only the
mode does not commit unrelated pending settings. Branding and background
mutations require the configuration app's write permissions. No shared
Unsplash credential is shipped.

## Reverted Trials

The following experimental page adapters were removed after router trials:

- Logs: restore original fonts, line spacing and filter layout.
- Realtime graphs/connections: restore original metrics, filters and table layout.
- Processes: restore original command and action layout.
- nftables: remove the new rule-table, set/list, verdict-map and palette overrides.
- Repository-key colors: remove new color overrides while retaining the current
  TableSection/PEM layout improvements.

These rollbacks do not change firewall rules, repository keys or the canonical
page-identity fix. Existing baseline styles for those pages are preserved.

## Deferred Work

- Full 3.x stylesheet/icon/font replacement would discard local customizations.
- Integrating the config app into the theme changes package ownership and
  requires a migration; keep the independent package.
- Official online updates install upstream packages over this customized fork.
  A fork-specific release feed and packaging strategy are required first.
- Dashboard and plugin rewrites depend on semantic tokens and installed plugin
  versions. Port against actual markup rather than copying entire modules.
- The broad Safari workaround changes overflow, transforms and containment on
  entire plugin subtrees and needs page-specific testing.
- Potential later work includes current package-manager controls, SSH-key UX,
  password-strength contrast and scoped status-dialog improvements.

## Verification And Limits

Local validation covers JavaScript/JSON/CSS syntax, menu and appearance behavior,
CSS cascade/selector matching and image/palette validation. The final CSS suite
has 21 checks, including comparisons with pre-trial styles for reverted pages,
original repository-key colors, retained key/interface/file-browser layouts,
and upstream appearance-control dimensions, pointer targets and menu styling.

Router validation covers template/module compilation, shell syntax, route
identity, asset hashes and authenticated page-shell responses. Ten additional
route-context cases ran with the router's Ucode interpreter. Earlier RPC tests
covered invalid inputs, background/branding upload/reset and appearance-mode
transactions while restoring existing settings. The final CSS deployment
confirmed unchanged configuration, pending UCI changes and repository keys.

Browser automation could not initialize. Screenshot, pixel-layout and complete
interactive SPA/back-button checks remain unverified. HTTP page shells and
CSS fixtures are not browser QA. No new SDK package build is claimed.

## Sources

- https://github.com/jerrykuku/luci-theme-argon/commit/5b2e62b
- https://github.com/jerrykuku/luci-theme-argon/commit/216d9ef
- https://github.com/sbwml/luci-theme-argon/tree/4b8b784
- https://github.com/jerrykuku/luci-theme-argon/blob/v3.1.0/RELEASE.md
- https://github.com/jerrykuku/luci-theme-argon/commit/0546f97
- https://github.com/jerrykuku/luci-theme-argon/commit/7da0555
- https://github.com/jerrykuku/luci-theme-argon/commit/056e9b7
- https://github.com/jerrykuku/luci-theme-argon/commit/f2c7dcd
- https://github.com/jerrykuku/luci-theme-argon/commit/8538bc6
- https://github.com/jerrykuku/luci-theme-argon/commit/8652e78
- https://github.com/jerrykuku/luci-theme-argon/commit/1055e79
- https://github.com/jerrykuku/luci-theme-argon/commit/6945d29
- https://github.com/jerrykuku/luci-theme-argon/commit/4a4eaab
- https://github.com/jerrykuku/luci-theme-argon/commit/6d9dd1a
- https://github.com/jerrykuku/luci-theme-argon/commit/57a9cd2
