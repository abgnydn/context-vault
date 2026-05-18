# CLAUDE.md — context-vault

Electron app that gives Claude Desktop / Cursor / browser AI **read-only** access to a local folder via stdio MCP + WebRTC signaling. See `README.md` for the product overview.

## 🎯 Resume here (on "continue")

_Updated: 2026-05-18 — Electron + MCP bridge + WebRTC signaling all functional. README + LICENSE in place. No CI yet._

**Steps (next, in priority order):**

1. **Get an automated CI build going.** `.github/workflows/release.yml` triggering `electron-builder` for macOS/Windows/Linux on tagged releases. Today the build is "run `npm run build` on a mac." That blocks distributing to non-mac users.
2. **Signing.** macOS builds run with `CSC_IDENTITY_AUTO_DISCOVERY=false` — users see a "damaged app" warning. Get a Developer ID cert + notarization in the release workflow.
3. **First-run UX polish.** `welcome.html` is functional but feels prototype-y. A 3-screen onboarding (pick folder → choose integrations → review block patterns) would close more "what do I do now?" loops.
4. **MCP tool surface review.** Currently exposes `list_documents`, `get_document`, `search_docs`. The MCP ecosystem is moving — check if there are conventional tool names other clients expect (e.g., `read_file` vs `get_document`).

**Acceptance for this Resume:** Tagged release on GitHub auto-builds signed macOS .dmg + Windows .exe + Linux .AppImage. First-run flow is polished. Tool surface aligns with one or two real MCP clients besides Claude Desktop / Cursor.

## Layout

```
main.js                   Electron main (tray + lifecycle)
signaling.js              WebRTC signaling server on :4445
mcp-bridge.js             stdio MCP server (real surface)
mcp-bridge-testable.js    pure-logic variant with injected I/O
privacy-dashboard.html    in-app dashboard
welcome.html              first-run picker
test/e2e.js               end-to-end test
assets/                   tray + app icons
scripts/                  build helpers (build.sh, prebuild.js)
```

## Working agreement

- **Read-only is load-bearing.** Never expose a write tool. If a feature requires write access, it goes in a separate app, not this one.
- The block-list defaults are conservative on purpose — `.env`, `.pem`, `.git/`, `node_modules/`. Don't relax them without a stated reason.
- Every file access lands in the audit log. No exceptions, no debug-mode skips. The audit log is the privacy contract.
- No telemetry. No "anonymous usage stats." Nothing.
