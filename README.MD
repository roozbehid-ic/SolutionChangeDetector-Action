# Solution Change Detector Action
`SolutionChangeDetector-Action` is a GitHub Action that leverages the [SolutionDependencyAnalyzer](https://github.com/roozbehid-ic/SolutionDependencyAnalyzer) library to parse Visual Studio solution (.sln) files, identify project dependencies, and output a list of required folders. 
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

# Usage
Look into [.github/workflows/simple-test.yml](.github/workflows/simple-test.yml) for more comprehensive usage.

``` yaml
jobs:
  analyze-dependencies:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
        with:
          fetch-depth: 0
          sparse-checkout: . # if you use any more other than file you can jsut opt to not checkout any file to make things super fast

      - name: Run Solution Dependency Analysis
        uses: https://github.com/roozbehid-ic/SolutionChangeDetector-Action@main
        with:
          solution_path: 'path/to/your/solution.sln'
          repo_path: github.workspace # Optional: if you want to provide anothre repository path
          operation_mode: 'internal_git' # Optional : Options: external_git, internal_git, combined_git, file
          git_id_path_filepath: 'id_path_file.txt' # Optional can be used to boost performance of internal_git mode on huge monorepos
          binary_folder_path: 'path/to/where/store/or/load/binaries' # Optional in case you want to precache binaries or store them in special folder
          download_as_nuget: true # Optional by default package is downloaded from nuget, if you say false here you have to provide it someway to this
```

# Outputs
Action will produce following outputs which you can use with syntax of `${{ steps.id_of_step.outputs.dependency_list }}`.
- dependency_list: Lists folders required by the solution, which can be used for sparse checkouts or as inputs to subsequent build or deploy steps.

# License
This project is licensed under the MIT License.