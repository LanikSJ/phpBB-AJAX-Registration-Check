# Agent Rules & Project Standards for phpBB-AJAX-Registration-Check

## Agent Identity

You are a senior phpBB extension developer working on this repository. Be
pragmatic, security-conscious, and conservative: prefer minimal, well-scoped
changes that follow existing project patterns, and explain any deviation.

## Glossary

- **AJAX (Asynchronous JavaScript and XML)**: technique for exchanging data
  with the server without reloading the page
- **PHP (PHP: Hypertext Preprocessor)**: the server-side scripting language
  phpBB and this extension are written in
- **GPL (GNU General Public License, version 2.0)**: the project's
  open-source license
- **XSS (Cross-Site Scripting)**: injection of malicious scripts into pages
  viewed by other users
- **POST (HTTP POST method)**: HTTP method used to submit data to the server
- **ALL (every applicable item)**, **MUST (required)**, **NEVER (in no
  case)**, **ALWAYS (in every case)**: requirement keywords per RFC 2119;
  absolute terms mean "without exception unless a documented,
  reviewer-approved escape hatch applies"

## Tooling

- **markdownlint**: Validates all markdown files. Run `markdownlint <file>`
  before committing any `.md` change; auto-fix with `markdownlint --fix`.
- **composer**: PHP dependency management for the extension
  (`composer install`, `composer validate`).
- **git**: Version control. Check `git status --short` before committing;
  keep the working tree free of scratch files.

## Repository Overview

phpBB-AJAX-Registration-Check is a phpBB extension that validates registration
form data via AJAX, checking username availability, email validity, and
password strength in real time before form submission.

## Project Structure

```text
pcgf/ajaxregistrationcheck/
├── composer.json          # Extension metadata, version, dependencies
├── license.txt            # GNU General Public License v2.0 (GPL, see Glossary)
├── config/
│   ├── routing.yml        # AJAX controller route definition
│   └── services.yml       # Service container definitions
├── controller/
│   └── controller.php     # AJAX endpoint for username/email validation
├── event/
│   └── listener.php       # Event listener for registration form data
├── language/
│   ├── de/                # German translations
│   ├── en/                # English translations
│   ├── pl/                # Polish translations
│   └── pt_br/             # Brazilian Portuguese translations
└── styles/
    └── all/
        ├── template/
        │   ├── event/     # Template event hooks
        │   └── javascript/ # Client-side validation logic
        └── theme/         # CSS styling
```

## Code Standards and Practices

### PHP Standards

- Write clean, maintainable PHP following phpBB extension development
  conventions
- Use phpBB's service container for dependency injection (request, db, user,
  config, template, helper)
- Use phpBB's `sql_escape()` and `utf8_clean_string()` helpers for
  database queries; if a different safeguard is used instead (e.g.,
  parameterized statements through the dbal), note it in the PR description
- Encode all values embedded into templates or JavaScript with `json_encode()`
  using `JSON_HEX_TAG | JSON_HEX_APOS | JSON_HEX_QUOT | JSON_HEX_AMP` to
  prevent XSS
- Use phpBB's `request->variable()` for all user input retrieval
- Validate AJAX requests with `request->is_ajax()` and check the request
  method before processing
- Keep the controller lean; delegate logic to services where appropriate

### JavaScript Standards

- Write clean, maintainable JavaScript following modern ES best practices
- Use jQuery consistently with phpBB's bundled jQuery
- Guard against script execution order issues (e.g., Cloudflare Rocket Loader)
  by waiting for required globals before initializing
- Use `setCustomValidity()` for native HTML5 form validation integration
- Keep client-side validation as UX feedback only; do not rely on it for
  security enforcement, unless an exception is documented and approved by a
  maintainer

### Documentation Standards

- Include clear installation and usage instructions
- Document phpBB and PHP version requirements
- Provide language-specific documentation where applicable
- Use markdown formatting consistently

### Markdown Compliance Requirements (MANDATORY)

- **ALL markdown files (.md) MUST pass markdownlint validation** with zero
  errors or warnings, except for rules explicitly disabled in
  `.markdownlint.json` or exemptions documented in the change description
- Run `markdownlint <filename>` on every markdown file before considering it
  complete
- Follow the project's `.markdownlint.json` configuration strictly
  (line length **200** characters per `MD013`; first-line heading rule `MD041`
  disabled)
- Common requirements include:
  - Maximum line length of 200 characters (MD013)
  - Consistent heading styles and hierarchy
  - Proper list formatting and indentation
  - Blank lines around headings and code blocks
  - Consistent link and reference formatting
  - No trailing whitespace
  - Files must end with newlines (only exception: files where the format
    inherently forbids it)
  - Proper table formatting when applicable
- Use `markdownlint --fix <filename>` for auto-fixable issues when available
- Validate markdown files in CI/CD pipelines where applicable

## Development Guidelines

### When Making Changes

- Preserve existing functionality unless explicitly asked to change it
- Update documentation when adding new features
- Test changes against supported phpBB versions (>= 3.3 < 3.4.0@dev)
- **Always run markdownlint and fix all issues in markdown files before
  considering changes complete**, unless an exemption is documented in the
  change description (see Markdown Compliance above)

### Temp File Cleanup (MANDATORY)

- Keep scratch, probe, and validation files out of the repository
  working tree; use `/tmp` unless a task genuinely requires files
  elsewhere (e.g., fixtures consumed by a repo test)
- Prefix scratch files so they are easy to attribute (e.g., `probe-*.js`)
- Remove all temp files as the final step of every task (before the
  completion report), including:
  - scratch scripts, dumps, and captured output files
  - temporary tool installs (e.g., a `/tmp/<dir>` with `node_modules`
    used for PHP syntax validation)
- Do not delete anything you did not create; leave pre-existing `/tmp`
  files and system entries (`zeb_def_ipc_*`, `node-compile-cache`,
  `zsh-fzf-tab-*`, `MozillaUpdateLock-*`) untouched, unless the user
  explicitly asks for a deeper cleanup
- Verify both cleanups before reporting completion:

  ```bash
  find /tmp -maxdepth 1 -newermt '<today> 00:00' -not -path './zeb*'
  git status --short
  ```

- Keep the repository working tree free of scratch files; if `git
  status` shows any, remove them before committing (intentional,
  reviewable changes aside)

### Extension Standards

- Maintain compatibility with phpBB 3.3.x and PHP >= 7.1.3
- Keep the extension self-contained within the `pcgf/ajaxregistrationcheck/`
  directory
- Use phpBB's template event system for style integration
- Support all existing languages (de, en, pl, pt_br) when adding new language
  strings
- Follow phpBB extension development best practices and coding standards

### Security Considerations

- Never commit sensitive information (API keys, tokens, passwords) — if one is
  required for local development, keep it in untracked environment files
- Always escape database queries with phpBB's `sql_escape()`, except where an
  equivalent safeguard (e.g., prepared/parameterized statements) is documented
- Always encode output embedded in JavaScript to prevent XSS, unless the value
  is provably safe (e.g., a validated integer) and the reason is documented
- Server-side validation is authoritative; client-side checks are UX only
- Do not store, log, or transmit sensitive user data

## GitHub & Automation Standards

### Commit Message Convention

- Use the conventional commit format: `type(scope): description`
- Common types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`
- Commit descriptions should be a bullet list of changes made
- Example:

  ```text
  docs(AGENTS.md): update agent rules for phpBB-AJAX-Registration-Check

  - this file had the wrong data from a totally different repository
  ```

#### Commit Types

- **feat**: A new feature
- **fix**: A bug fix
- **docs**: Documentation only changes
- **style**: Code formatting changes that do not affect meaning — for
  example: white-space adjustments, semicolon changes, or quote-style
  switches
- **refactor**: Code change that neither fixes a bug nor adds a feature
- **perf**: Performance improvement
- **test**: Adding or correcting tests
- **chore**: Changes to build process or auxiliary tools

#### Scope Guidelines

- **controller**: AJAX controller logic
- **listener**: Event listener logic
- **template**: Template files and event hooks
- **language**: Language/translation files
- **docs**: documentation
- **ci**: CI/CD configuration
- **deps**: dependency updates

These rules apply specifically to files in `.github/*` (workflows, templates,
and documentation).

### Quality Gates (MANDATORY)

Before completing any change in `.github/`:

1. ✅ Run `markdownlint` validation (if .md file).
2. ✅ Ensure project standards are followed.
3. ✅ Verify contribution guidelines are up-to-date.
4. ✅ Check that automation maintains project standards.

### Templates and Workflows

- Ensure issue and pull request templates provide clear, actionable
  guidelines.
- Include project-specific troubleshooting sections in templates.
- Reference existing project documentation and standards.

### Documentation standards in .github/

- The `.github/CONTRIBUTING.md` file is expected to cover the following; update
  it when any item is missing:
  - phpBB extension development environment setup instructions.
  - Testing requirements and procedures.
  - Documentation standards for new features.
  - Project-specific contribution guidelines.

### Automation and CI/CD

- Project workflows should include automated testing stages; when a stage is
  impractical, record the reason in the workflow file.
- Integrate code quality checks into CI/CD; record any exception in the
  workflow file.
- Configure release automation properly; for an intentionally manual release
  process, keep the documentation in `.github/`.

### Error Prevention

- NEVER generate markdown that violates line length or formatting rules,
  unless the violation is required by content (e.g., long URLs) and a lint
  disable comment or documented exemption covers it.
- ALWAYS cross-reference with existing project practices before making
  changes; deviations require a documented rationale.
- ENSURE all links and references are valid and current.
- VALIDATE that new requirements don't conflict with established workflows.
