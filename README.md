# GitHub Action Template in Rust

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
