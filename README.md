# Webstudio Core Visual Builder

Webstudio is an open-source, browser-based visual website builder. This
repository contains the Builder application and the packages it needs to load,
edit, preview, save, and publish structured website projects.

The project is focused on the production website-building workflow: you own
your website data, components, and infrastructure, and can use the hosted
workflow or self-host it.

## What You Can Build

- Load existing Webstudio project data and render it in the visual Canvas.
- Add, select, edit, duplicate, nest, reorder, and delete components with
  drag-and-drop support.
- Configure component properties, CSS styles, breakpoints, interactive states,
  transitions, and supported animations.
- Create pages, routes, metadata, dynamic routes, templates, reusable
  components, design tokens, and global styles.
- Bind components to resources and dynamic data, including collection/repeat
  rendering.
- Use website interactions supported by the runtime, including navigation,
  anchor scrolling, forms, and dialogs.
- Manage images, fonts, and other assets.
- Preview, save, reload, build, export, and publish projects.

## Current Scope

The Marketplace browsing and listing UI has been removed from this checkout to
keep the product focused on the visual builder. Existing project marketplace
metadata remains supported during project loading and synchronization for
backward compatibility; it is not exposed as Builder UI.

Authentication, project permissions, project synchronization, and publishing
remain in place because they are required by the current project load/save and
deployment workflows.

## Repository Layout

| Path | Purpose |
|---|---|
| `apps/builder` | Remix-based visual Builder and Canvas application. |
| `packages/project` | Project persistence and project-domain types. |
| `packages/project-build` | Runtime generation, build, export, and publishing support. |
| `packages/css-engine`, `packages/css-data` | CSS editing, style serialization, and responsive styles. |
| `packages/sdk*` | Component definitions, rendering SDK, and component registries. |
| `packages/content-engine`, `packages/expression`, `packages/query-builder*` | Dynamic data, expressions, bindings, and collection queries. |
| `packages/sync-client`, `packages/protocol` | Editor state synchronization and Builder protocol. |
| `docs` | Product, contribution, self-hosting, and integration documentation. |

## Prerequisites

- Node.js 22
- pnpm 9.14.4 or later within the 9.x range

Install dependencies from the repository root:

```sh
pnpm install
```

## Development

Start the Builder in local development mode:

```sh
pnpm dev
```

Useful validation commands:

```sh
pnpm --filter=@webstudio-is/builder typecheck
pnpm test:builder:core
pnpm test:builder:app
pnpm e2e:builder:build
pnpm build
```

Some Builder workflows require the configured local backend, authentication,
and database services. See the Builder scripts in
[`apps/builder/package.json`](./apps/builder/package.json) and the
[documentation](./docs/README.md) for environment-specific instructions.

## Project Data Compatibility

Webstudio does not treat a website as one unstructured JSON blob. A project is
made up of structured, synchronized data including pages, component instances,
properties, assets, style sources and rules, breakpoints, resources, data
sources, and project settings. Preserve these relationships when integrating
with external storage or creating import/export adapters.

## License

- **Webstudio core** (all functionality in this repository) is free/open
  source under AGPL-3.0-or-later.
- **`sdk-components-animation`** is optional and proprietary. Accept the
  [Webstudio, Inc. EULA](./packages/sdk-components-animation/LICENSE) before
  using it.
