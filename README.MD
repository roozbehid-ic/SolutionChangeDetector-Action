# SolutionChangeDetector-Action
SolutionChangeDetector-Action is a GitHub Action that leverages the SolutionDependencyAnalyzer library to parse Visual Studio solution (.sln) files, identify project dependencies, and output a list of required folders. 
This list can be used to perform sparse checkouts, detect changes, or determine if a solution requires rebuilding, making it ideal for monorepo and large-repo environments.

# Features
- Dependency Detection: Parses a .sln file and gathers all dependency folders used across the solution’s projects.
- No full Checkout requirements: You can run this without full clone which will save big time on monorepos
- Selective Checkout: Outputs folders required for the solution, enabling sparse checkout or selective builds.
- Change Detection: Detects if any files in the solution have changed, useful for automated build triggers.

# Use Cases
- Sparse Checkouts: Automatically identify and checkout only the folders required for a specific solution.
- Selective Build Triggers: Trigger builds only when solution dependencies are modified.
- Build Optimization: Reduce build times by only including necessary dependencies.

# Getting Started
## Pre-Requisites
This action requires your solution file path and your repository to be available in the workflow context. Ensure you have a Visual Studio solution file (.sln) in your repository.

Usage
Add SolutionChangeDetector-Action to your GitHub Actions workflow:

``` yaml

name: Dependency Analysis Workflow

on:
  push:
    paths:
      - '**.sln'  # Trigger when solution file changes

jobs:
  analyze-dependencies:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Run Solution Dependency Analysis
        uses: https://github.com/roozbehid-ic/SolutionChangeDetector-Action@v1
        with:
          solution_path: 'path/to/your/solution.sln'
          git_mode: 'internal' # Options: internal, external, system
          id_path_filename: 'id_path_file.txt' # Optional for internal mode
```

# Inputs
- solution_path (required): Path to the .sln file in your repository.
- git_mode (optional): Specifies the mode for dependency retrieval:
- internal: Uses an internal Git library, fastest if id_path_filename is provided.
- external: Uses Git executable for dependency analysis.
- system: Uses regular file access.
- id_path_filename (optional): File to provide additional Git data for internal mode (e.g., git ls-tree --format "%(objectname),%(path)" -r HEAD).

# Outputs
dependency_folders: Lists folders required by the solution, which can be used for sparse checkouts or as inputs to subsequent build or deploy steps.
Example Workflow with Output

``` yaml
- name: Run Solution Dependency Analysis
  id: analyze
  uses: https://github.com/roozbehid-ic/SolutionChangeDetector-Action@v1
  with:
    solution_path: 'path/to/your/solution.sln'
    git_mode: 'internal'

- name: Display Dependency Folders
  run: echo "Folders: ${{ steps.analyze.outputs.dependency_folders }}"
```

# License
This project is licensed under the MIT License.