# Agent Rules & Project Standards for phpBB-AJAX-Registration-Check

## Repository Overview

phpBB-AJAX-Registration-Check is a phpBB extension that validates registration
form data via AJAX, checking username availability, email validity, and
password strength in real time before form submission.

## Project Structure

```text
pcgf/ajaxregistrationcheck/
├── composer.json          # Extension metadata, version, dependencies
├── license.txt            # GPL-2.0 license
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
- Always use phpBB's `sql_escape()` and `utf8_clean_string()` helpers for
  database queries
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
- Keep client-side validation as UX feedback only; never rely on it for
  security enforcement

### Documentation Standards

- Include clear installation and usage instructions
- Document phpBB and PHP version requirements
- Provide language-specific documentation where applicable
- Use markdown formatting consistently

### Markdown Compliance Requirements (MANDATORY)

- **ALL markdown files (.md) MUST pass markdownlint validation**
  with zero errors or warnings
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
  - Files must end with newlines
  - Proper table formatting when applicable
- Use `markdownlint --fix <filename>` for auto-fixable issues when available
- Validate markdown files in CI/CD pipelines where applicable

## Development Guidelines

### When Making Changes

- Preserve existing functionality unless explicitly asked to change it
- Update documentation when adding new features
- Test changes against supported phpBB versions (>= 3.3 < 3.4.0@dev)
- **Always run markdownlint and fix all issues in markdown files before
  considering changes complete**

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

- Never commit sensitive information (API keys, tokens, passwords)
- Always escape database queries with phpBB's `sql_escape()`
- Always encode output embedded in JavaScript to prevent XSS
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
- **style**: Formatting (white-space, etc)
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

- `.github/CONTRIBUTING.md` must include:
  - phpBB extension development environment setup instructions.
  - Testing requirements and procedures.
  - Documentation standards for new features.
  - Project-specific contribution guidelines.

### Automation and CI/CD

- Project workflows must include automated testing stages.
- Code quality checks must be integrated into CI/CD.
- Release automation must be properly configured.

### Error Prevention

- NEVER generate markdown that violates line length or formatting rules.
- ALWAYS cross-reference with existing project practices before making
  changes.
- ENSURE all links and references are valid and current.
- VALIDATE that new requirements don't conflict with established workflows.
