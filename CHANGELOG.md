# Changelog

All notable changes to contextd-marketplace will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

## [0.3.0] - 2025-12-26

### Added
- **Production Mode Configuration**: Comprehensive production mode architecture
  - `CONTEXTD_PRODUCTION_MODE` environment variable for production deployment
  - `CONTEXTD_LOCAL_MODE` for local development (bypasses auth/TLS)
  - Security validations: blocks NoIsolation mode, requires auth/TLS in production
  - YAML + environment variable configuration precedence
- **Security Enhancements**:
  - Path validation: blocks traversal attacks (`/data/../../../etc/passwd`)
  - Environment variable validation: hostname (command injection), path (traversal), URL (scheme restriction)
  - TOCTOU race mitigation with f.Stat() on open file descriptors
  - Configuration file security: permissions (0600/0400), symlink resolution, size limits (1MB)
- **Version Management System**:
  - `VERSION` file as single source of truth
  - `scripts/sync-version.sh` for automated version synchronization
  - `VERSIONING.md` comprehensive workflow documentation (113 lines)
- **Documentation**:
  - `SECURITY.md` - Vulnerability reporting, security features, best practices (118 lines)
  - Coordinated disclosure policy (90-day)
  - Security feature documentation (multi-tenant isolation, secret scrubbing, input validation)
- **Infrastructure**:
  - `.github/workflows/test.yml` - CI/CD with Go 1.24/1.25 matrix, linting, coverage
  - 80+ new tests: production config (9), env validation (4), path validation (4)
  
### Fixed
- **CRITICAL**: Path traversal detection now works (replaced dead code with depth-based comparison)
- **CRITICAL**: Test environment variable corrected (`PRODUCTION_ENABLED` → `CONTEXTD_PRODUCTION_MODE`)
- Production config loading now preserves YAML configuration (no unconditional override)
- Environment variable validation prevents injection attacks
- Configuration path validation prevents symlink escapes

### Changed
- Configuration precedence: Environment > YAML > Defaults (properly enforced)
- All 36 config tests passing with comprehensive coverage

### Security
- Fixed path traversal vulnerability in `validatePath` function
- Added command injection protection for hostname validation
- Added URL scheme restriction (http/https only)
- Production mode blocks NoIsolation to prevent tenant data leakage


- `repository_search` tool schema (input/output definitions)
- `branch` field to `repository_index_input` schema
- `branch` and `collection_name` fields to `repository_index_output` schema
- `/init` command - Initialize contextd for new/existing projects with Kinney CLAUDE.md framework
- Git commit re-index protocol in `session-lifecycle` skill

### Changed
- **BREAKING**: Skills now prioritize `repository_search` over Read/Grep/Glob
  - `using-contextd`: Added "Code Search Priority" section establishing search order
  - `cross-session-memory`: Updated learning loop to search code first, then memories
  - `session-lifecycle`: Added re-index triggers after git commit and session end
- Updated `using-contextd` skill to include `repository_search` tool
- Session end protocol now includes `repository_index` before `memory_record`

## [0.1.0] - 2025-12-04

### Added
- Initial release
- MCP tool schemas for all contextd tools:
  - `checkpoint_save`, `checkpoint_list`, `checkpoint_resume`
  - `memory_search`, `memory_record`, `memory_feedback`
  - `remediation_search`, `remediation_record`
  - `repository_index`
  - `troubleshoot_diagnose`
- Skills:
  - `using-contextd` - Overview of contextd tools
  - `session-lifecycle` - Session start/end protocols
  - `cross-session-memory` - Learning loop workflow
  - `checkpoint-workflow` - Context preservation at 70%
  - `error-remediation` - Error resolution flow
- Slash commands:
  - `/search` - Search memories and remediations
  - `/remember` - Record a learning
  - `/checkpoint` - Save checkpoint
  - `/status` - Show contextd status
  - `/diagnose` - AI-powered error diagnosis
  - `/resume` - Resume from checkpoint
  - `/troubleshoot` - Error diagnosis
  - `/auto-checkpoint` - Auto-save before clear
  - `/context-check` - Check context usage
- Skill file format schema (`skill_frontmatter`, `skill_file`)
