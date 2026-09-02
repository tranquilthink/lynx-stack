---
applyTo: "packages/genui/lynx-xml/**,packages/genui/server/agent/{lynx-xml-agent,html-fragment-to-main-thread-script-tool}.ts,packages/genui/server/service/lynx-xml-agent.ts"
---

Keep Lynx XML prompt construction in `packages/genui/lynx-xml`; the server
agent should consume the package and contain only provider and Agent wiring.
Use the pinned direct `@lynx-js/skill-vanilla-lynx` dependency as the source of
shared Element PAPI, lifecycle, event-routing, background, and styling guidance.
Select and sanitize its Markdown in `src/vanilla-lynx-skill.ts`, import it with
`?raw`, and inline it at build time. Do not duplicate that guidance in the local
prompt or add runtime filesystem reads. Keep the dependency bundled in Rslib
and in Rstest's `output.bundleDependencies` so the raw-asset rule processes its
Markdown. Adapting the skill must not introduce Rspeedy, external bundles, or a
required `globalThis.processData` contract into the self-contained `.lynxml`
prompt without an explicit product decision.
Require the page root and every container that lays out Element children to
apply a class with explicit `display: flex` and `flex-direction`; generated XML
must not rely on Lynx's default Linear layout.
Keep numeric component ids separate from Element PAPI node references. Both
arguments to `__AppendElement` must be nodes, append helpers must receive the
parent node rather than its id, and `pageId` is reserved for page-owned element
creation APIs.
Keep the Lynx XML agent's `html_fragment_to_main_thread_script` tool limited to
deterministic initial-tree conversion. Parse well-formed fragments in source
order, emit Element PAPI creation, literal property, and append calls that assume
`page` and `pageId` already exist, and leave state, event handlers, updates,
lifecycle registration, cleanup, and CSS to the agent-authored artifact.
Bound element nesting before recursive emission, ignore whitespace-only text
nodes, and preserve the original text of every non-empty node. Cover both
limits with converter regression tests.
Keep generated fragment JavaScript in a Mastra `RequestContext` registry scoped
to one agent run. Return only an opaque placeholder plus XML-id node bindings to
the model, require the placeholder exactly once on its own line, and expand it
before final artifact validation and delivery. Never store generated scripts on
the cached Agent or in a process-global registry.
Maintain mobile-specific defaults in `src/mobile-design.ts`: start from a
narrow portrait, single-primary-scroll layout; use responsive units and
semantic tokens; consume each safe-area edge once; reserve space for fixed
bars; and require legible type and touch targets of at least 44px by 44px. When
content can exceed one viewport, make the definite-height vertical scroll view
the first business node directly below the Page and do not wrap it in another
business view. Keep that concrete Element PAPI and scroll-view API contract in
`src/prompt.ts`; `src/mobile-design.ts` should contain only provider-neutral
mobile design intent. Explicit user design systems may override visual
defaults, but not Lynx layout, safe-area, runtime, or accessibility contracts.
Keep prompt construction deterministic and cover configurable fields and core
dependency-derived and local override invariants with package tests. Verify the
built output has no runtime Markdown imports. Include the package config in the
root Rstest project list and validate it with `pnpm exec rstest run -c
rstest.config.ts --project genui/lynx-xml`.
