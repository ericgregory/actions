# Actions

Actions toolkit for WebAssembly projects.

## Features

- **Complete CI/CD pipeline** for WebAssembly components
- **Attestation and SBOM support** for supply chain security
- **Multi-registry support** with configurable registries
- **Multiple tagging strategies** for flexible release management
- **Monorepo friendly** with working directory support

## Composite Actions

| Name                                                       | Purpose                                                                                                                   |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| [setup-wash-action](./setup-wash-action)                   | Setup wash CLI for GitHub workflow (Published to [GitHub Marketplace](https://github.com/marketplace/actions/setup-wash)) |
| [setup-wash-cargo-auditable](./setup-wash-cargo-auditable) | Configure cargo-auditable for wash builds with SBOM generation                                                            |
| [wash-build](./wash-build)                                 | Build WebAssembly components using wash CLI and output the artifact path                                                  |
| [wash-oci-publish](./wash-oci-publish)                     | Publish Wasm components to OCI registries with optional attestation support                                               |
| [install-cross](./install-cross)                           | (Deprecated) Installs the cross-compilation tool `cross` from a branch that supports macOS targets                        |

## Example Usage

For a complete example of using these actions together, see the [test workflow](./.github/workflows/test.yml).

### Basic Build and Publish Workflow

```yaml
jobs:
  build-and-publish:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      packages: write
      attestations: write
      id-token: write

    steps:
      - uses: actions/checkout@v4

      - name: Setup wash CLI
        uses: wasmcloud/setup-wash-action@main
        with:
          wash-version: "1.0.0"

      - name: Setup cargo-auditable
        uses: wasmcloud/setup-wash-cargo-auditable@main
        with:
          attestation: "true"

      - name: Build component
        id: build
        uses: wasmcloud/wash-build@main

      - name: Publish component
        uses: wasmcloud/wash-oci-publish@main
        with:
          component_path: ${{ steps.build.outputs.component_path }}
          registry: ghcr.io
          attestation: "true"
          image_tags: latest,v1.0.0,${{ github.sha }}
```
