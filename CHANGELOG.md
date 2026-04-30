# Changelog

All notable changes to the ACPS Workflow extension will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-04-29

### Added

- 11 workflow commands: setup, create-epic-backlog, release-plan, plan-bridge, test, bugfix, uat, docs, scope, release, change-request
- Scope counting command (BCP full, BCP simplified, FP+SNAP modes)
- Prompt pack for BCP/FP/SNAP assessment (ported from ScopeCounting)
- Memory files: ACPS methodology reference, state machine, BCP rubric
- Configuration template with quality gates and counting settings
- Hooks: after_tasks (plan-bridge), after_implement (test), after_specify (count)
