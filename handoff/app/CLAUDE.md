Frontend rules (owned by Dzhamal). Full design: docs/design.md.

Scope: app/, components/, lib/ui/. An AP clerk checks a freight invoice against what was agreed before paying: a dense, calm, auditable worklist, not a landing page or a chat.

## Contract and ownership
- lib/types.ts (spec §5.1) is the final shared contract. Never edit it, never define parallel domain types. Changes need a team message.
- Do not add dependencies (ask Pranav in the team chat). Do not edit convex/, lib/engine/, lib/ai/, fixtures/, scripts/ or the root CLAUDE.md.
- Map spec types to UI only in lib/ui/view-model.ts: supported, outside_quote_scope → Cleared; potential_discrepancy, invoice_noncompliance → To dispute; needs_review → Waiting on answers; userVerdict "false_positive" → Cleared + tag "Dismissed by clerk".
- Roles are exactly "clerk" | "controller" | "ops_lead" (Clerk / Controller / Ops lead). Role lives in localStorage ("cargoos.role", try/catch, fallback clerk); a valid ?role= overrides it once. Pass role to every mutation.
- Why-line = Finding.explanation, rendered verbatim. Never compose or edit why text. Source links come from Finding.evidence.
- Totals only from caseTotals() in lib/engine. Never re-implement money math. Show "+ Waived by clerk $X" only when X > 0. Money via formatUSD / parseMoneyToCents; integer cents.
- What changed = payload.changes of "recomputed" events. Event types: only the spec EventType list.
- Data: fixtures/scenarioA.ui.json (?fixture=1) or Pranav's Convex query; keep fixture mode working.
- Rule badges (names are proposals, confirm with Dev): R0 Matches agreed rate, R1 Duplicate, R2 Rate mismatch, R3 Currency, R4 Allowed by quote, R5 Outside quote scope, R6 No evidence, R7 Sources disagree, R8 Approved, R9 Rejected.

## Tokens and type
- Colors only via the tokens in app/globals.css. No raw hex in TSX, no Tailwind palette colors.
- Color = status only. link = anything leading to a source, focus, selection, the one primary button. highlight = a quoted span inside a document, nothing else.
- Status is never color alone: group + chip fill (To dispute solid bg-dispute text-white; Waiting bg-waiting-tint with dashed border-waiting-bar; Cleared no fill) + SVG glyph (⊗ / dashed ? / ✓) + bar (solid / dashed / none) + word. No literal ✓ ⊗ characters.
- Radius 6 containers, 4 controls, 3 tags, 2 sheet. No pills. Border OR shadow; shadow only on hover-card, tooltip, toast.
- Public Sans for UI, money (tabular-nums), rule codes, locators, chat times. Source Serif 4 for email, reply, ops question, dispute body. IBM Plex Mono ONLY for quote / freight_invoice / proforma text (white-space: pre) and the event-type chip in History. Never mono for UI labels.
- Theme sizes only (meta 13, why 14, body 15, title 17, figure 22, hero 34, doc 15, typed 12). Weights 400/500/600. Sentence case.

## Layout
- Design for 1280×640, check 1280×720. Revision 1 fits in 620px. The list column has its own scroll (motion.div layoutScroll).
- One grid template (--cols) for column header, group headers and rows. Rows live in ONE list surface with group bands. Never cards.
- Source view covers the inspector: 40px header, 24px provenance line, 36px chip row, one scroll area with every evidence document and its <mark>. findSpan: exact, then whitespace-normalized. Not found: no fake highlight, show "Quote not found in this document. Nothing is highlighted."
- Clerk: Agree / Not an issue always visible on To dispute and Waiting rows. Controller and ops lead never see them. Ops lead never sees approve or dispute controls.
- No modals. Disabled = aria-disabled + visible reason. Clerk's Approve still calls the mutation; the server writes approval_blocked.
- Toasts bottom-right, only for Not an issue, approval events, reply added, evidence added, copy.

## Motion
- One authored motion: the revision player (components/case/use-revision-player.ts). Diff on-screen groups vs new data by Finding.key; move one row at a time (560ms, ease [0.2,0,0,1], no spring); then the equation (NumberFlow); then open What changed. Close Source view when a revision starts. Never render Convex row updates directly.
- Clerk's own Not an issue / edit: one 280ms move, no dimming.
- Flat motion.li list, layout="position", stable keys, no overflow-hidden on ancestors, MotionConfig reducedMotion="user". No other animation.
- Keys: only j, k, Esc, ignored in inputs. No <kbd> hints.

## Copy
- Spec strings exactly: Agree, Not an issue, Draft dispute, Request approval, Approve, Requires controller, "Synthetic data. Nothing is sent or paid automatically.", "1 to dispute · 2 waiting on answers · 5 cleared.", "Checked before you pay."
- Names only from spec §7 (Pacific Crest Logistics, Bayline Home Goods, Q-PCL-0902, PCL-INV-1012, "Bayline 40HC Ningbo-LB"). People: seeded user names or role words ("the ops lead").
- Middle dot only in the count line. Deltas signed (+$300, U+2212 minus). Dates "Sep 5" / "Oct 24, 2:05 PM PT", timeZone "America/Los_Angeles". Curly quotes around verbatim quotes.
- Facts say "Extracted by Claude", decisions "Decided by rule R2". The ops lead's button is "Reply" (nothing is sent). Never "AI", "smart", "magic", "insights", "seamless", "Submit", confidence %, invented metrics, emoji.

## Process (every component)
1. Plan first: ASCII at 1280px, tokens, what each element tells the clerk; check against Never.
2. Build against the fixture with real Scenario A content.
3. Component test for anything showing numbers or gating an action (spec §10.4, values from §7.2).
4. npm run lint:slop; fix or justify every hit. Screenshots (npm run shots) only at the 3 checkpoints.
5. Commit on your own branch.

## Never
Gradients, glow, backdrop-blur, dark mode, cream backgrounds, KPI card rows, icons in colored circles or on every label, Sparkles/robot icons, chat bubbles, ALL CAPS, 01/02/03 markers, border-l-4 callouts, rounded-xl+, shadow + border, Inter, Geist, mono UI labels, lorem/Acme, unlabeled spinners, fake highlights, invented types or event names, client-side money math.
