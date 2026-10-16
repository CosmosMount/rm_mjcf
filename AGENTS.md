# RM MJCF asset component instructions

## Common engineering standard

Apply `.agents/docs/engineering.md` for the shared engineering process. This
file is the local adapter for the runtime asset repository; it does not inherit
root integration ownership.

## Asset boundaries

- Keep this repository limited to runtime MJCF, referenced meshes, textures and
source/provenance metadata. Conversion scripts, previews, experiment logs and
unreferenced source CAD belong outside the asset repository.
- Preserve relative asset paths and each model's directory contract. A model
must load from a fresh clone without relying on an absolute path in the parent
workspace.
- Keep visual and collision semantics explicit. Visual geometry or a static
preview does not establish a certified collider, target or physical contact
model.
- Record source CAD/mesh identity and hashes when changing an asset. Do not
silently replace an upstream model or change licensing/provenance claims.

## Verification

For an asset change, load every affected XML entry from a clean component path,
check that all referenced files exist, and report visual-only versus collision
validation separately. Do not infer flight or controller behavior from an XML
parse alone.
