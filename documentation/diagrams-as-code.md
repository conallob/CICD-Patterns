# Diagrams-As-Code

Living diagrams, for things such as system architectures are best tracked in a format that is approachable by non experts (for minor fixes) and facilitates collaboration. 
Following the Documentation-As-Code philosophy, these diagrams should reside close to the codebase, allowing for updates in sync alongside code changes. One of the 
most common tools is [Graphviz](http://www.graphviz.org)

## Examples

### GitHub Actions

```
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

TODO(conallob): Find a way to generate output on PRs, not just on releaes
TODO(conallob): Find a way to upload generated content somewhere other than a release

### Azure DevOps Pipelines

TODO(conallob): Add ADO example here, once it works

## See Also

* https://kroki.io
* 
