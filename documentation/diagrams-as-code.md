# Diagrams-As-Code

Living diagrams, for things such as system architectures are best tracked in a format that is approachable by non experts (for minor fixes) and facilitates collaboration. 
Following the Documentation-As-Code philosophy, these diagrams should reside close to the codebase, allowing for updates in sync alongside code changes. One of the 
most common tools is [Graphviz](http://www.graphviz.org)

## Examples

### GitHub Actions

```yaml
on:
  push:
    branches:
      - "main"

# Input: example.dot
# Output: example.png

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      # Prepare build environment
      - name: Checkout
        uses: actions/checkout@v3

      # Install Graphviz
      - name: Setup Graphviz
        uses: ts-graphviz/setup-graphviz@v1

      # Generate PNG from DOT code  
      - name: Generate DOT Diagram
        run: dot -Tpng example.dot -o example.png
        
      # Add generated content to Release
      - name: Upload Generated Diagrams
        uses: softprops/action-gh-release@v1
        if: startsWith(github.ref, 'refs/tags/')
        with:
          files: |
            example.png
```

TODO(conallob): Find a way to generate output on PRs, not just on releases

TODO(conallob): Find a way to upload generated content somewhere other than a release

### Azure DevOps Pipelines

```yaml
resources:
  repositories:
    - repository: Example/Repository
      type: githube
      name: Example-Repository
      endpoint: "GitHub"


stages:
- stage: 'DiagramsAsCode'
  displayName: 'Generate Diagrams from ASCII definition'

  jobs:
  - job: GeneratePNGImage
    displayName: Generate PNG Images
    container:
      image: bash:latest
    steps:
    - checkout: self
    - task: Bash@3
      displayName: 'Install Graphviz'
      inputs:
        targetType: 'inline'
        script: |
          sudo apt-get update && sudo apt-get install --yes graphviz
    - task: Bash@3
      displayName: 'Generate PNG Image for each DOT File'
      inputs:
        targetType: 'inline'
        script: |
          for i in $(find . -name *.dot); do dot -Tpng ${i} -o $(Build.ArtifactStagingDirectory)/$(basename $i ".dot").png ; done
    - task: GithubRelease@0
      displayName: 'Upload to GitHub'
      inputs:
        gitHubConnection: github
        repositoryName: Example/Repository
        assets: $(Build.ArtifactStagingDirectory)/*.png
        target: '$(Build.SourceVersion)'
        tag: $(githubTag)
        addChangeLog: true
        assetUploadMode: replace
```

TODO(conallob): Find a way to generate output on PRs, not just on releases

## See Also

* https://kroki.io
* 
