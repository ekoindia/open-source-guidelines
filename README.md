# Guide for Creating and Managing Open-Source Software

<a href="https://eko.in" target="_blank">![Eko.in](https://img.shields.io/badge/Develop%20with-Eko.in-brightgreen)</a>
<a href="https://twitter.com/intent/tweet?text=Wow:&url=https%3A%2F%2Fgithub.com%2Fekoindia%2Faeps-gateway-lib" target="_blank"><img alt="Twitter" src="https://img.shields.io/twitter/url?style=social&url=https%3A%2F%2Fgithub.com%2Fekoindia%2Faeps-gateway-lib"></a>
<a href="https://twitter.com/intent/follow?screen_name=ekospeaks" target="_blank">![Twitter Follow](https://img.shields.io/twitter/follow/ekospeaks?label=Follow&style=social)</a>

## Overview

This guide provides detailed steps for creating a new open-source project or for open-sourcing existing closed projects. The guide also includes a comprehensive checklist for quality control, a curated list of tools, and best practices for ongoing maintenance. Additionally, it suggests metrics to measure the progress of your open-source efforts, and offers guidance on engaging contributors and building an active community.

Open-sourcing your projects involves much more than just publishing code. It requires thoughtful preparation, a structured repository setup, robust documentation, community engagement, and a commitment to long-term maintenance. The benefits of open-sourcing include fostering innovation, gaining community contributions, and enhancing your brand within the developer ecosystem. This guide will help you effectively navigate all aspects of the open-sourcing journey.

---

## Step-by-Step Process for Open-Sourcing Existing Services

### 1. Evaluation and Preparation

- **Audit Code Quality**: Evaluate the current state of each service. Conduct code quality reviews to ensure consistency, modularity, and maintainability. Ensure that the codebase adheres to internal coding standards before making it publicly accessible.
- **Identify Sensitive Components**:
  - Audit for sensitive information such as **API keys**, **passwords**, **user data**, or **confidential business logic** that must be removed or sanitized.
  - Create a checklist of sensitive items that must be refactored or removed prior to release.
- **Decouple Proprietary Dependencies**: Identify and remove proprietary components or third-party integrations that cannot be made open-source.
  - Consider creating open alternatives or stubs for these components to ensure the project remains functional.
- **License Selection**:
  - Decide on an open-source license that aligns with your business goals, such as **MIT** (permissive), **Apache 2.0** (permissive with patent protection), or **GPL** (copyleft).
    - **GPL** may be preferred to ensure all derivative works are also open-source, along with explicit provisions regarding patent grants.
    - **MIT** may be preferred for its simplicity and flexibility, including incorporation into proprietary projects.
  - Consult legal advisors to ensure compliance with relevant regulations and avoid risks related to intellectual property.

### 2. Documentation

- **Create a README File**: This is often the first point of interaction for potential contributors or users. Make it engaging and informative. Include:
  - **Project Overview**: Explain what the project does, why it exists, and what problems it solves.
  - **Features and Roadmap**: Describe key features, current progress, and planned future improvements.
  - **Installation Guide**: Provide clear, step-by-step instructions for setting up the development environment.
  - **Contribution Guidelines**: Describe how others can contribute, including code style, branch conventions, and steps to submit pull requests.
- **Code Comments**: Ensure that each module is thoroughly commented to explain its purpose and any critical logic that might be non-intuitive.
- **API Documentation**: Use free and open-source tools like **Swagger** or **Redoc** to generate thorough and interactive API documentation, especially for backend services utilizing **SpringBoot** or **Node.js**.
- **Changelog and History**: Maintain a changelog to track what changes have been made in each version.

### 3. Setting Up Repositories

- **Repo Naming Conventions**: Maintain consistent, descriptive naming conventions for GitHub or GitLab repositories to ensure clarity. Include the project name, the type of service, and its purpose in the repository name.
- **Branch Strategy**:
  - Establish and communicate a clear branching strategy. Main branches should include `main` (for stable releases) and `dev` (for ongoing development).
  - Use **feature branching** for new features or major changes, and **hotfix branches** for urgent fixes.
  - Tag stable releases appropriately to provide checkpoints for developers who want to work with specific versions.
- **Configuration Management**:
  - Store configuration separately from code, utilizing **environment variables** or free configuration management tools like **Dotenv** or **Confd**.
  - Provide template configuration files (e.g., `.env.example`) to help developers set up quickly.

### 4. Security and Compliance

- **Dependency Management**: Regularly audit dependencies to prevent vulnerabilities. Use free tools like [**OWASP Dependency-Check**](https://github.com/jeremylong/DependencyCheck) or [**npm audit**](https://docs.npmjs.com/cli/v9/commands/npm-audit) to identify outdated packages and potential security risks.
- **Code Scanning**: Integrate static analysis tools such as [**SonarQube Community Edition**](https://www.sonarsource.com/open-source-editions/sonarqube-community-edition/) or [**CodeQL**](https://codeql.github.com) (both available for free) into your CI/CD pipeline to identify security flaws and improve code quality.
- **Data Sanitization**: Carefully scrub any sensitive customer data or proprietary information from the codebase and sample data files. Use mock data wherever possible.
- **Vulnerability Disclosure Policy**: Publish a clear policy on how security vulnerabilities should be reported by external developers or users.

### 5. Setting Up CI/CD for Open Source

- **Automated Tests**: Implement automated testing to ensure code stability. Use **Jest** for Node.js, **JUnit** for Java, and **Cypress** for React components to create unit and integration tests. These tools are open source and have generous community support.
- **Continuous Integration**: Use free-tier or open-source tools like **GitHub Actions**, **Travis CI** (free for open-source projects), or **Jenkins** to automate the testing and deployment of services when changes are merged.
- **Code Quality Tools**: Enforce coding standards using **ESLint** for JavaScript/TypeScript and **PMD** for Java to catch common issues early. Both tools are open source and widely adopted.
- **Release Automation**:
  - Use tools like [**semantic-release**](https://semantic-release.gitbook.io/) to automate versioning and changelog generation based on commit messages. **Semantic-release** is free and integrates well with GitHub.
  - Automate deployments for critical services, including release candidates, through your CI/CD pipeline using free-tier CI tools.

### 6. Docker-Readiness for Development and Production

Ensuring Docker-readiness is critical for both development and production environments. Docker enables consistent environments, making it easier for both contributors and production systems to replicate and maintain your services. Below are key considerations for Docker-readiness:

- **Dockerfile Creation:** Create and maintain a Dockerfile for each service. Ensure that the Dockerfile follows best practices, such as minimizing image size and using multi-stage builds where applicable.
- **Docker Compose for Development:** Provide a `docker-compose.yml` file to make it easy for developers to spin up the necessary services locally.
- **Production-Ready Docker Image:** Ensure that the Docker image used in production is secure, minimal, and optimized for performance.
- **Environment Configuration:** Use environment variables to manage configuration, and ensure they are properly documented in .env.example files.
- **Testing in Docker:** Integrate Docker-based testing within your CI/CD pipeline to ensure consistency across environments.
- **Docker Hub or Registry:** Publish images to Docker Hub or a private container registry, and tag releases for consistency.

### 7. Building a Contributor Community

- **Contribution Guidelines**: Write a CONTRIBUTING.md file to guide external contributors. Define standards for code quality, testing, and submission processes.
  - Encourage contributions with examples such as **fixing typos**, **documenting features**, or **adding tests**.
- **Code of Conduct**: Add a code of conduct, such as **Contributor Covenant**, to foster a welcoming and inclusive community.
- **Label Management**: Use GitHub Labels to organize issues and make it easy for contributors to understand which tasks are available. Examples include:
  - **Good First Issue**: For beginners.
  - **Help Wanted**: For more complex problems that need assistance.
  - **Bug** and **Feature Request**: Clearly categorize issues.
- **Documentation for New Contributors**: Create a "Getting Started" guide to help developers set up their environment and understand the project architecture. This can help reduce onboarding time for new contributors.

### 8. Post-Launch Management

- **Issue Triage**: Regularly triage open issues to prioritize bug fixes and new features. Assign issues to internal team members or mark them as **Help Wanted** to encourage community participation.
- **Merge Reviews**: Set up a team rotation for reviewing pull requests, ensuring consistent quality and encouraging prompt feedback.
- **Automated Release Notes**: Use free tools like **release-drafter** to automatically generate release notes that summarize new features, fixes, and other key updates in each release.
- **Community Engagement**: Regularly interact with the community through GitHub Discussions, Q&A forums, and other communication channels.

---

## Checklist for Open-Sourcing

-

---

## Tools for Managing Open Source Projects

### Free and Open-Source Tools

- **[OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/)**: Free tool for dependency vulnerability scanning to detect issues early.
- [**SonarQube (Community Edition)**](https://www.sonarsource.com/open-source-editions/sonarqube-community-edition/): Free code quality and security scanning for continuous improvement.
- [**Swagger/OpenAPI**](https://swagger.io/tools/open-source/): API documentation tools for comprehensive and interactive documentation.
- [**Jest**](https://jestjs.io), [**JUnit**](https://junit.org/junit5/), [**Cypress**](https://www.cypress.io): Automated testing frameworks for ensuring code stability. All are free and open-source.
- **[Release-Drafter](https://github.com/release-drafter/release-drafter)**: Automatically draft release notes to keep stakeholders updated, available for free.
- **[Docusaurus](https://docusaurus.io)**: Free and open-source tool to create and maintain high-quality project documentation with ease.
- **[CodeQL](https://codeql.github.com/)**: Free static code analysis and vulnerability detection, integrated with GitHub.
- **[Prettier](https://prettier.io)**, **[Checkstyle](https://checkstyle.sourceforge.io)**: Open-source code formatters and linters to ensure consistency
- **[Discord](https://discord.com)**: For real-time community engagement, it is free and open-source-friendly.

### Recommended Tools (May Not Be Free)

- **[Snyk](https://snyk.io)**: Advanced security vulnerability scanning for dependencies. It offers a free tier but has premium features for more in-depth scanning and reporting.
- **[Travis CI](https://travis-ci.com)**: While free for open-source projects, paid plans are available for additional features, especially for private repositories.
- **[SonarQube (Enterprise Edition)](https://www.sonarqube.org/enterprise-edition/)**: Provides advanced features like deeper analysis, enterprise-level support, and more integrations compared to the free community version.

---

## Metrics to Track Open-Source Progress

### 1. Project Conversion Metrics

- **Percentage of Services Open-Sourced**: Measure the number of services that have been made open-source compared to the total services in scope.
- **Code Readiness Score**: Assign a readiness score (1-10) for each service based on code quality, documentation, and security, providing a sense of how close each project is to being public-ready.
- **Checklist Completion**: Track progress by counting completed checklist items for each service.

### 2. Community Engagement Metrics

- **Stars and Forks**: Track the popularity and reach of each project using stars and forks as a measure of interest.
- **Issues and Pull Requests**: Monitor the number of open, closed, and merged PRs. A healthy balance indicates community interest and active management.
- **New Contributors**: Measure the number of first-time contributors as an indicator of new community involvement.
- **Contributor Retention**: Track how many contributors return after their first contribution, indicating a welcoming and productive environment.
- **Discussions and Feedback**: Measure the level of interaction in GitHub Discussions or other community channels.

### 3. Code Quality Metrics

- **Test Coverage**: Use tools like **SonarQube** to measure test coverage for each service. Aiming for at least **80% coverage** is generally recommended.
- **Security Vulnerabilities**: Track vulnerabilities found and fixed over time using tools like **[OWASP ZAP](https://www.zaproxy.org)** (free) or **[Snyk](https://snyk.io/)** (offers a free tier for open-source projects).
- **Code Quality Score**: Measure code quality through static analysis tools to track improvements over time.

### 4. Community Support Metrics

- **Issue Resolution Time**: Track the average time taken to respond to and close issues, providing insights into how responsive the project maintainers are.
- **Pull Request Response Time**: Measure the time taken to review and merge pull requests, as this affects contributor satisfaction.
- **User Satisfaction Surveys**: Periodically survey users and contributors to gather feedback and identify areas for improvement.

---

## Best Practices for Managing Open-Source Software

### 1. Code Quality and Readability

- **Keep It Simple**: Aim for simplicity and maintainability. Write clean, well-documented code that is easy to understand and modify. Avoid overly complex architectures unless absolutely necessary.
- **Consistent Style**: Enforce consistent coding styles using tools such as **Prettier** for JavaScript/TypeScript and **Checkstyle** for Java. This ensures that all contributions maintain a consistent standard.
- **Modular Design**: Structure the codebase in a modular fashion, making it easier for contributors to understand and work on individual parts without having to comprehend the entire system.

### 2. Contributor Friendliness

- **"Good First Issue" Tags**: Mark issues as **"Good First Issue"** to help newcomers identify tasks suitable for getting started. These should be well-defined, self-contained, and relatively easy to complete.
- **Mentorship Programs**: Encourage internal developers to mentor new contributors. This can involve code reviews, answering questions, and pairing on difficult tasks.
- **Office Hours**: Host virtual office hours or regular Q&A sessions to address questions and help contributors.

### 3. Community Interaction

- **Communication Channels**: Use platforms like **Discord** (free and open-source-friendly) for real-time interactions. Actively monitor these channels to provide prompt support and foster an active community.
- **Transparent Roadmaps**: Share project roadmaps and future plans to give contributors visibility into where the project is heading. Solicit feedback from the community to align priorities.
- **Recognition and Rewards**: Acknowledge contributions through contributor recognition programs. Highlight top contributors in release notes or blog posts, and consider providing small rewards such as swag or badges.

---

## Eko Project Inventory for Open-Sourcing

To effectively manage the migration closed services to open-source, use the following inventory template to track each project:

- **Project Name**: Provide the name of the project.

- **Business Category**: Classify the project under categories such as **EPS**, **Eloka**, **Connect**, **Android**, **Libraries**, **Others**, etc.

- **Short Description**: A brief overview of the project's purpose and functionality.

- **URL to Codebase**: Link to the repository (e.g., GitHub or GitLab URL).

- **Codebase Location**: Specify whether the code is in **Azure Repo** or **GitHub**.

- [] **Open-Source**: Indicate if the project is currently open-source (**yes/no**).

- **Active Project**: Indicate if the project is actively maintained (**yes/no**).

- **Code Quality Status**: Assign a code quality status (e.g., good, needs improvement) based on the initial audit.

- **Documentation Readiness**: Specify if key documents (README, contribution guidelines, API docs) are complete (**yes/no**).

- **License Chosen**: Indicate the type of license selected for the project.

- **Sensitive Data Check**: Confirm whether sensitive data has been removed (**yes/no**).

- **Dependency Status**: Note whether dependency management is up-to-date and free from vulnerabilities.

- **Docker-Readiness:** Confirm if Dockerfiles are available and the project is Docker-ready for both development and production environments (**yes/no**).

- **Community Engagement Level**: Track engagement metrics such as stars, forks, or contributors (if open-source).

- **Checklist Completion**: Indicate how many checklist items have been completed for the project.

- **Initial Release Date**: Record the date when the project was initially released as open-source.

- **Contributor Onboarding**: Note whether onboarding materials are available for contributors (**yes/no**).

- **CI/CD Implementation**: Confirm whether CI/CD pipelines are set up for automated testing and releases (**yes/no**).

This inventory will help track each project's status, identify what is needed to migrate each service to open source, and measure the progress of the open-sourcing process.

---

## Next Steps

- **Initial Pilot**: Begin with a pilot by open-sourcing a simpler service. Use the experience gained to refine your open-sourcing process.
- **Team Workshops**: Conduct internal workshops to align all developers and stakeholders on open-source best practices, goals, and expectations.
- **Community Outreach**: Announce the first open-source project launch through social media, blog posts, and developer forums. Encourage developers to contribute and get involved.
- **Continuous Improvement**: Regularly revisit and update this guide to reflect evolving best practices and lessons learned from your ongoing open-source initiatives.

