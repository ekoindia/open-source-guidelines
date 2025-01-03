# How To Contribute To This Project

Thank you for considering contributing to our project! We welcome all kinds of contributions, including fixing typos, documenting features, and adding tests. This document outlines the process to help you get started.

## Branching Model

We use a simplified Git branching model to manage our development workflow:

- **main** (or, master): The stable branch containing the latest release.
- **dev** (or, develop): The main development branch where the next release is prepared.
- **feature/**: Branches for new features.
- **bugfix/**: Branches for bug fixes.
- **hotfix/**: Branches for critical fixes to the current release.

## Branching and Merging Guidelines

1. **Branch from**:
   - For new features: `develop`
   - For bug fixes: `develop`
   - For hotfixes: `main`

2. **Merge into**:
   - Feature branches: `develop`
   - Bugfix branches: `develop`
   - Hotfix branches: `main` (and then merged into `develop`)

## Code Style Guidelines

Please follow these guidelines to ensure consistency in our codebase:

- Use meaningful variable and function names.
- Write comments to explain complex logic.
- Follow the project's existing code style and formatting.
- Use linting tools to check your code before submission.

## Git Commit Messages

- use lowercase for commit messages
- Use the present tense ("add feature" not "Added feature")
- Use the imperative mood ("move cursor to..." not "Moves cursor to...")
- Limit the first line to 72 characters or less
- Reference issues and pull requests liberally after the first line
- Start the message with one of these prefixes:
  - `fix:` for bug fixes
  - `feat:` for new features
  - `docs:` for documentation changes only
  - `style:` for formatting changes
  - `refactor:` for code refactoring
  - `test:` for adding missing tests
  - `chore:` for maintenance and other tasks
  - `ci:` for CI/CD changes
  - `security:` for security updates
  - `config:` for configuration changes
- Separate the subject from the body with a blank line
- Example: `feat: add feature XYZ to improve user experience`

## Testing Guidelines

- Write unit tests for new features and bug fixes.
- Ensure all tests pass before submitting a pull request.
- Use the existing testing framework and follow the project's testing conventions.

## Submission Process

1. Fork the repository and create your branch from the appropriate base branch (eg: `dev` or `develop`).
2. Make your changes, ensuring your code adheres to the guidelines above.
3. Write or update tests as needed.
4. Push your branch to your forked repository.
5. Submit a pull request to the appropriate branch (`dev` or `develop`).

## Code Of Conduct

Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project, you agree to abide by its terms.

## A quick note to new contributors
- As a new contributor, you might have noticed that some issues are labeled as `needs triage`.
  - When an issue is labeled as "Needs Triage," it indicates that the issue has been submitted but has not yet been reviewed or assigned to a specific team member for further action.
  - If the issue does not have a Needs Triage label it means that the issue is reviewed by the specific team member.

We appreciate your contributions and look forward to collaborating with you!