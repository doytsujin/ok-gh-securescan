# GitHub Action Template in Rust

This repository is tailored as a template for creating GitHub Actions specifically designed for Rust development projects. It encompasses a suite of configuration files and scripts that automate and standardize various aspects of developing, testing, and maintaining Rust-based GitHub Actions. This documentation provides an overview of the repository's components, emphasizing its purpose as a template for Rust developers.

Key Components:
Cargo Configuration (Cargo.toml & cargo-generate.toml):

Cargo.toml establishes the baseline configuration for a Rust project, including package information and Rust version specifications.
cargo-generate.toml is configured for cargo-generate, facilitating the instantiation of new Rust projects from this template. It includes settings for dynamic placeholders, pre-generation hooks, and file management.
GitHub Actions Workflows:

The repository includes several .yml workflow files, each serving a specific role in the lifecycle of a Rust GitHub Action.
integration_tests.yml.liquid & rust_checks.yml.liquid: These workflows automate integration testing and code quality checks (like linting and formatting) specific to Rust.
release.yml.liquid: Manages the automated release process across multiple platforms, an essential aspect for GitHub Actions intended for a wide range of users.
test-template.yml: Aids in building and testing the template itself, ensuring its reliability and effectiveness as a starting point for other developers.
Rhai Script (pre-script.rhai):

Used for initial project setup, this Rhai script customizes the template for specific instances, such as setting project names and repository details interactively.
Renovate Configuration (renovate.json):

Automates dependency updates, keeping the GitHub Action and any derived projects up to date with the latest Rust packages and tools.
Purpose and Usage:
The repository is designed as a comprehensive template for Rust developers looking to create or maintain GitHub Actions. Its purpose revolves around:

Simplifying Initialization: Streamlining the setup of new GitHub Actions for Rust, providing a solid foundation that can be easily customized.
Ensuring Quality and Compatibility: Automated testing and linting workflows guarantee the quality of the GitHub Action and its suitability across different platforms.
Facilitating Releases: The release process is automated, making the distribution of updates or new versions of the GitHub Action seamless.
Maintaining Up-to-Date Dependencies: Dependency management is automated to ensure security and leverage the latest advancements in Rust packages.
Target Audience:
This repository is ideal for Rust developers or teams involved in creating or using GitHub Actions. It offers a robust starting point, reducing the initial overhead in setting up a new GitHub Action and ensuring best practices in continuous integration and deployment are followed.

Note for Third-Party Users:

Familiarity with Rust, GitHub Actions, and the respective tools used in this repository (like cargo-generate and Renovate) is essential for effective use and customization.
The repository is structured to provide a quick and efficient start for Rust-based GitHub Actions, emphasizing automated workflows and standardized practices.

## cargo-generate.toml Configuration File

This file configures how cargo-generate will scaffold a new Rust project from a template. It is crucial for ensuring the new project is set up according to specific requirements and standards.

Sections:
[template]

ignore: Specifies files or directories to be excluded from the generated project.
Example: [".github/workflows/test-template.yml", ".idea"] means these paths will not be included in the new project.
[hooks]

pre: Defines scripts or commands to be executed before the project generation process.
Example: ["pre-script.rhai"] indicates that the pre-script.rhai script will run before the project scaffolding.
[placeholders]

This section is used to define placeholders in the template that will be replaced with user-provided values during the generation process.
Each placeholder is defined under its own subsection, like [placeholders.github_org].
Placeholders Defined:
github_org
type: The data type expected for the placeholder, here it is a string.
prompt: The message displayed to the user to provide a value.
Example: "GitHub Organization (e.g., user name) for this action?"
description
type: Again, a string.
prompt: A message prompting for a brief description of the action's purpose.
Example: "What will this action do?"
Usage:
When you run cargo-generate to create a new project using a template, this configuration file guides the tool in customizing the project. It ensures certain files are ignored, pre-generation scripts are executed, and placeholders in the template are replaced with values you provide.

## action.yml.liquid Configuration File

This file is a template for creating a GitHub Action configuration (action.yml), using Liquid template syntax for dynamic content rendering.

Key Components:
Metadata:

name: The name of the GitHub Action, represented by {{project-name}}. Replace with the actual project name.
description: A brief description of what the GitHub Action does, denoted by {{description}}.
author: The author(s) of the GitHub Action, indicated by {{authors}}.
Inputs:

Defined parameters that the action can accept.
Example Inputs:
error:
description: A description of the input, e.g., "The error message to display, if any".
required: Indicates if the input is mandatory (false in this case).
default: The default value for the input ('' or empty string).
token:
description: Description of the token, typically for GitHub authentication.
required: This input is mandatory (true).
default: The default value is set to the GitHub workflow token (${{ github.token }}), using Liquid raw tags {% raw %} and {% endraw %} for escaping.
Outputs:

Defines what the action will produce as output.
Example Output:
error:
description: Describes any error that occurred.
value: The output value, here it's the error output of a step in the workflow, escaped with Liquid raw tags.
Usage:
When configuring a GitHub Action using this file, replace the Liquid template placeholders ({{placeholder}}) with actual values relevant to your GitHub Action.
The file should be named action.yml after replacing the placeholders.
This configuration defines how the GitHub Action behaves, what inputs it accepts, and what outputs it produces.

## pre-script.rhai - Project Initialization Script

This Rhai script is executed during the initial setup phase of a Rust project, specifically for customizing and setting up project files and variables.

Script Actions:
File Operations:

Deletes an existing file named README.md.
Renames a file from README-TEMPLATE.md to README.md.
Project Name Setup:

Checks if the variable project-name is already set.
If not set, it prompts the user to input a project name (advised in kebab-case).
The entered project name is then stored in the variable project-name.
GitHub Repository Setup:

Checks if the variable github_repo is already set.
If not set, prompts the user for the name of the GitHub repository for this action, with the default suggestion being the value of project-name.
The entered repository name is stored in the variable github_repo.
Usage:
This script should be executed before the generation of a new project using cargo-generate or a similar tool.
It helps in automating the renaming of template files and setting up project-specific variables.
The script is interactive and will prompt the user for input if necessary.

## renovate.json - Renovate Configuration File

This JSON file configures Renovate, a tool that automatically updates project dependencies, ensuring they are kept up-to-date and secure.

Key Components:
$schema:

Specifies the URL to the Renovate configuration schema. This helps with validation and auto-completion in supported editors.
Example: "https://docs.renovatebot.com/renovate-schema.json"
extends:

A list of shared configuration presets to extend.
"config:base": A basic configuration preset provided by Renovate.
":semanticCommitTypeAll(chore)": Ensures all commit messages are semantic and prefixed with chore.
"github>Turbo87/renovate-config//rust/updateToolchain": An external configuration from a GitHub repository, specifically for Rust toolchain updates.
packageRules:

Defines rules for managing updates to packages.
Example Rule:
matchUpdateTypes: Specifies which types of updates (e.g., minor, patch, pin, digest) to match.
automerge: If set to true, updates matching the above types are merged automatically.
Usage:
Place this file in the root directory of your project.
Configure or adjust the rules as per your project's dependency management needs.
Renovate will read this configuration and manage your project's dependencies accordingly, based on the rules defined.

## integration_tests.yml.liquid - Integration Tests Workflow for GitHub Actions
This YAML file, enhanced with Liquid template syntax, defines a GitHub Actions workflow specifically for conducting integration tests on a GitHub Action.

Key Components:
Workflow Name:

name: "Test consuming this action" - Identifies the purpose of the workflow.
Triggers:

on: Defines the events that trigger this workflow.
release: Triggers on a release event, specifically when a release is released.
workflow_run: Triggers after the completion of a specified workflow, here referenced as "Release".
Jobs:

test_success: A job to test the successful execution of the action.
runs-on: Specifies the runner environment, here ubuntu-latest.
steps: Lists the steps for the job.
Checks out the repository.
Uses the action located in the current directory (./).
test_error: A job to test the action's behavior on error.
Similar setup as test_success, but includes additional steps.
continue-on-error: Allows the workflow to continue even if this step fails.
Uses the action with a specified error input (error: "This is an error").
A step to verify the failure behavior.
Usage:
Replace Liquid placeholders in the file with actual values relevant to your GitHub Action.
Place this file in the .github/workflows directory of your repository.
The workflow will automatically run integration tests when specified triggers are met, ensuring your GitHub Action works as expected in different scenarios.

## release.yml.liquid - Automated Release Workflow for GitHub Actions
This YAML file, incorporating Liquid template syntax, defines a GitHub Actions workflow that automates the process of building and releasing software across different operating systems and architectures.

Key Components:
Workflow Name:

name: "Release" - This identifies the workflow's primary function.
Triggers:

on: Specifies the events that will trigger the workflow.
workflow_dispatch: Allows the workflow to be manually triggered from the GitHub UI.
inputs: Defines input fields for the manual trigger.
version: The version number for the release, marked as required.
Jobs:

build-artifacts: A job to build the software for different targets.
strategy: Defines the strategy for the build.
fail-fast: Set to false to continue other builds even if one fails.
matrix: Defines a matrix of build environments.
Includes various targets (like x86_64-unknown-linux-musl, x86_64-apple-darwin, x86_64-pc-windows-msvc) and corresponding operating systems.
Windows builds include an additional file_extension property (.exe).
runs-on: The runner environment for each matrix item is defined by the os field in the matrix.
Usage:
Replace Liquid template placeholders with actual values as needed.
Place this file in the .github/workflows directory of your repository.
The workflow facilitates building and releasing software across multiple platforms, triggered manually with a specified version.

## rust_checks.yml.liquid - Rust Project Checks Workflow for GitHub Actions
This YAML file, with Liquid template enhancements, defines a GitHub Actions workflow focused on conducting essential checks for a Rust project. These checks include unit tests, linting, and code formatting.

Key Components:
Workflow Name:

name: "Unit tests, linting, and formatting" - Clearly describes the workflow's purpose.
Trigger:

on: [push] - Specifies that the workflow is triggered on every push to the repository.
Jobs:

checks: The job encompasses several steps to ensure code quality.
runs-on: Specifies the runner environment, here ubuntu-latest.
steps: Lists the operations performed by the job.
Checkout: Checks out the source code.
Update Rust: Updates the Rust toolchain to the latest stable version.
Run tests: Executes unit tests using cargo test.
Lint: Runs linting with cargo clippy to identify potential issues.
Check formatting: Ensures code formatting is consistent using cargo fmt --check.
Usage:
Integrate this workflow into the .github/workflows directory of your Rust project's repository.
This file automatically performs crucial checks on each push, helping maintain code quality and consistency.
The workflow can be further customized to include additional checks or alter existing ones.

## test-template.yml - GitHub Actions Workflow for Building a Template

This YAML file defines a GitHub Actions workflow aimed at automating the process of building a software template, particularly useful in a Rust project environment.

Key Components:
Workflow Name:

name: "Build Template" - Indicates the main function of the workflow.
Triggers:

on: Specifies the conditions under which the workflow is executed.
workflow_dispatch: Allows manual triggering of the workflow.
pull_request: Triggers the workflow on pull requests.
merge_group: (This seems like a custom or project-specific trigger, might need further context or adjustment).
push: Triggers on pushes, specifically to the main branch.
Jobs:

build: The primary job in this workflow.
runs-on: Specifies the runner environment, here ubuntu-latest.
env: Defines environment variables, such as PROJECT_NAME.
steps: Lists the operations performed by the job.
Checkout: Checks out the source code.
Use cargo-generate: Uses the cargo-generate-action to scaffold a project based on a template.
with: Specifies parameters for the action, like the project name (${{ env.PROJECT_NAME }}).
env: Sets environment variables specific to cargo-generate, such as CARGO_GENERATE_VALUE_GITHUB_ORG and CARGO_GENERATE_VALUE_GITHUB_REPO.
Usage:
Place this file in the .github/workflows directory of your repository.
The workflow will automatically build your software template on the specified triggers, aiding in consistent and automated template generation.
Customize the triggers and steps according to your project's needs.
