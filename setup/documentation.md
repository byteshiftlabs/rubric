---
pack: task-workflows
summary: Template and guidance for writing thorough factual documentation.
tags: [setup, docs, writing]
---

# Documentation

Generate thorough documentation while keeping it factual and relevant.

## Prompt

```
Generate comprehensive documentation for [FILE_OR_MODULE] in [PROJECT_NAME].

Include:
- Module/file purpose and responsibilities
- Every public function/method with:
  - Description of what it does
  - Parameters (name, type, purpose)
  - Return values
  - Exceptions/errors raised
  - Usage example
- Dependencies and their roles
- Data flow explanation
- Edge cases and limitations

Be clear enough that a new developer can understand the code and work safely in it.
```

## Placeholders

- `[FILE_OR_MODULE]`: Target code to document
- `[PROJECT_NAME]`: Project context

## Variations

### README generation

This is the recommended template. Adapt sections as needed for the project.

```
Generate a README for [PROJECT_NAME] following this structure:

# Project Name
One-line description of what the project does.

[Badges: License, Language version, Dataset DOI if applicable]

> **Disclosure:** This software was developed with AI assistance under human supervision. [Include if applicable]

## Overview
2-3 paragraphs explaining the project purpose, key features, and what problem it solves.

## Quick Start
Step-by-step commands to clone, install, and run:
- Clone and setup
- Install dependencies
- Run the main script/program
Include actual bash code blocks.

## How It Works [for complex projects]
Explain the pipeline, algorithm, or architecture with subsections.

## Project Structure
Directory tree showing key files and their purposes.

## Requirements
- Language version
- Dependencies with purpose
- Optional dependencies

## License
License type with link.
```

### Inline comments
```
Add inline comments to [FILE] explaining complex logic, non-obvious decisions, and algorithm steps.
Every comment must describe what the code actually does, not what it should do.
```

## Tips

- Prefer enough documentation over sparse documentation, but keep each section focused
- Mark uncertain or incomplete sections clearly instead of inventing detail
- Include "why" explanations for non-obvious design choices
- Always keep documentation adjacent to code when possible
- Keep sections focused: testing goes in tests, architecture in architecture, etc. — do not mix concerns (see [shared-contract.md](../core/shared-contract.md))
- **Platform-specific documentation:** Only include installation commands and instructions for platforms where the software has been tested

## Keeping README.md and ROADMAP.md in Sync

After implementing changes to source code or documentation, always check whether **README.md** and **ROADMAP.md** need updating:

- **README.md**: Update if the change affects features, requirements, build instructions, project structure, usage examples, or any section that describes current capabilities.
- **ROADMAP.md**: Update if the change completes a planned item (mark as done), adds new planned work, or changes project direction. If the repository does not have a ROADMAP.md, raise a GitHub issue to track its creation.

Do this check as part of every commit that changes functionality or documentation, not as a delayed follow-up task.
