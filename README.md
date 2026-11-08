# agent-browser-playwright

> A Codex skill for coordinating agent-browser exploration with Playwright verification, one browser controller at a time.

[![Experimental](https://img.shields.io/badge/status-experimental-orange?style=for-the-badge)](#project-status) [![Developed and tested on Codex only](https://img.shields.io/badge/validated%20on-Codex%20only-6e40c9?style=for-the-badge)](#project-status) [![MIT License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)](LICENSE)

This is an instruction-only skill. It helps an agent choose and coordinate browser tools; it does not install, bundle, or run them for you.

## Project status

This skill is experimental. It was developed and tested under Codex only; other agent hosts have not been validated. The combined agent-browser-to-Playwright handoff depends on host support: never run both controllers at once, and report inconclusive if the harness cannot release control and observe the declared oracle.

## Workflow

| Task | Browser driver | What counts as verified |
| --- | --- | --- |
| Known, repeatable flow with fixed acceptance criteria | Playwright alone | The declared Playwright assertions pass. |
| Unfamiliar, goal-based task | agent-browser as the sole controller in isolated staging or with disposable test data | After exploration ends, an independent Playwright assertion in a fresh context or an API/backend oracle succeeds. |
| Outcome visible only in the current page | agent-browser, then a supported, sequential, read-only Playwright handoff | The declared read-only oracle succeeds after agent-browser releases control; a mismatch is a verified failure. Report inconclusive only when no safe oracle or supported handoff can observe the result. |

Never control the same browser context with agent-browser and Playwright at the same time. An agent's completion report or screenshot is evidence, not a pass condition.

## Tool capabilities

| Tool surface | Useful capabilities | Best fit and limits |
| --- | --- | --- |
| agent-browser CLI | Goal-directed browser control; accessibility snapshots and element references; navigation, interaction, waits, text/value/attribute reads; screenshots; tabs; console/network inspection; request routing and HAR export; named sessions. Snapshot/output controls can reduce returned context. | Explore an unfamiliar site or route while maintaining one controller. Domain and action restrictions are opt-in; verify the effective settings before navigation. |
| Playwright library / Test | Role, label, text, and test-id locators; actions with auto-waiting; retrying web assertions; isolated contexts; fixtures and projects; network/API mocking; traces and test reports. | Run fixed flows repeatedly and create durable UI regression tests. |
| Playwright MCP | Tool-based navigation, snapshots, search, actions, forms, keyboard input, waits, tabs, screenshots, and console/network inspection. Optional assertions, storage, tracing, video, and other tools depend on the MCP configuration. | Interactive inspection or verification using the tools exposed by the host. MCP calls alone do not create a durable Playwright Test suite. |

For agent-browser, read version-matched instructions with `agent-browser skills get core` before first use and after a CLI version change. Playwright Test/library and Playwright MCP are different surfaces; confirm which one is available before choosing a flow. Do not use `browser_run_code_unsafe` for this workflow.

## Install

### Install the skill directly

After this repository is published, use Codex's built-in `$skill-installer` and provide this skill directory:

```text
https://github.com/katniis/agent-browser-playwright/tree/main/skills/agent-browser-playwright
```

The GitHub owner is `katniis`. For manual user-scope installation in PowerShell, run from a clone of this repository:

```powershell
$sourcePath = (Resolve-Path -LiteralPath '.\skills\agent-browser-playwright\SKILL.md').Path
$skillPath = Join-Path $HOME '.agents\skills\agent-browser-playwright'
[System.IO.Directory]::CreateDirectory($skillPath) | Out-Null
$destinationPath = Join-Path $skillPath 'SKILL.md'
[System.IO.File]::Copy($sourcePath, $destinationPath, $false)
```

For a project-scope install, copy `skills/agent-browser-playwright/` to that project's `.agents/skills/agent-browser-playwright/` directory. Restart Codex if the skill does not appear. See the [Codex skills guide](https://developers.openai.com/codex/skills).

### Install through the Codex plugin marketplace

After publication, add the repository marketplace:

```text
codex plugin marketplace add katniis/agent-browser-playwright
```

Then open the Plugins Directory in the Codex desktop app, choose this marketplace, and install `agent-browser-playwright`. The marketplace metadata is in `.agents/plugins/marketplace.json`; its plugin points at the repository root `plugin.json`, which discovers the same skill under `skills/`.

A GitHub/repo marketplace makes this plugin available through that source. Listing in the universal public Plugins Directory is a separate submission and review process; this repository has not been submitted there.

## Requirements and safety

- Install or configure agent-browser, the Playwright library/Test runner or Playwright MCP, and the required browsers separately. This repository bundles none of them.
- For unfamiliar exploration, use an isolated staging environment or disposable test data. Confirm target-domain and action restrictions are active; agent-browser does not enable them by default, and its allowlist is not a network-egress firewall.
- Stop state-changing agent-browser actions before verification. Record the goal, app/build, final URL, completion status, relevant snapshot or screenshot, and errors. Redact secrets and sensitive data.
- Prefer an independent Playwright assertion in a fresh context or an API/backend oracle. Use a current-page handoff only after agent-browser releases control and only when the harness supports sequential, read-only inspection. If no safe oracle can observe the result, report it as inconclusive.
- Read the complete [skill instructions](skills/agent-browser-playwright/SKILL.md) before relying on the workflow. See the [Agent Skills specification](https://agentskills.io/specification).

## Contributing

For a proposed workflow change, include the task type, expected oracle, and scenario evidence that the change preserves exclusive browser control and independent verification. Keep the one canonical skill file in `skills/agent-browser-playwright/SKILL.md` so direct skill and plugin distribution cannot drift.

## License

This project is licensed under the [MIT License](LICENSE).
