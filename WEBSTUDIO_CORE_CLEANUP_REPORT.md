# Webstudio Core Cleanup Report

## A. Features Removed

| Feature | Actual removed modules/files | Dependencies updated | Tests | Result |
|---|---|---|---|---|
| Marketplace browsing and listing UI | `apps/builder/app/builder/features/marketplace/*`; page marketplace section | Removed the Marketplace sidebar tab, its panel, page-settings listing controls, and template-settings prop | Builder TypeScript check; `git diff --check` | Removed. Marketplace project data remains readable for compatibility. |

The removal was made from clean revision `e4d143171d2e936a2829887643cd94c5924f24c7`; this is the rollback checkpoint. The files changed for this removal are the files shown by `git diff --stat`.

## B. Features Retained

| Feature | Actual module/files | Reason required |
|---|---|---|
| Builder shell and visual panels | `apps/builder/app/builder`, `routes/_ui.(builder).tsx` | Editor UI and project authorization/load entry point. |
| Canvas, selection and drag/drop | `apps/builder/app/canvas`, `canvas/shared/use-drag-drop.ts` | Visual rendering, selection, insertion, moving, nesting and reordering. |
| Component tree, properties and styles | `builder/features`, `shared/instance-utils`, `packages/css-engine`, `packages/css-data` | Core no-code component editing and CSS serialization. |
| Pages, templates, routing and preview | `builder/features/pages`, `shared/pages`, `project-build` | Required page CRUD, routing and reusable structures. |
| Data binding and collections | `packages/content-engine`, `packages/expression`, `packages/query-builder`, `shared/resources` | Required dynamic data and repeat behavior. |
| Assets and fonts | `builder/features/assets`, `packages/asset-uploader`, `packages/fonts`, `packages/image` | Required media references and upload flow. |
| Persistence and synchronization | `shared/sync`, `packages/project`, `packages/sync-client`, `packages/protocol` | Loads, mutates, saves and reloads complete editable state. |
| Preview, build and publishing | `packages/project-build`, `shared/project-settings/section-publish.tsx`, `services/api-build.server.ts` | Required published rendering, CSS/runtime output and deployment pipeline. |
| Authentication and authorization | `app/auth`, `services/auth-strategy`, `authorization-token`, `wsauth` | No safe replacement exists in this checkout. |

## C. Features Not Removed

| Feature | Module/files | Reason | Remaining dependency |
|---|---|---|---|
| Marketplace data/server API | `shared/marketplace`, sync stores, project-build marketplace fields | Existing projects and template/build payloads contain marketplace metadata; removal would require an explicit migration. | Sync/load/import and project-build compatibility. |
| Plans, billing and permissions | `packages/plans`, `shared/permissions`, builder loader | Plan features gate access and workspace/project permissions. | Authorization path and builder loader. |
| Sharing, invitations and collaboration | authorization token, workspace/permission and sync modules | Shared editing permissions are interwoven with persistence/synchronization. | Project access and sync protocol. |
| OAuth providers | `app/auth`, Remix auth packages | No replacement authentication path was established. | Required safe access controls. |
| Hosting/provider support | Vercel/Remix configuration and publishing services | At least one functional publishing path must remain; not independently verified removable. | Production build/deploy flow. |
| Documentation, fixtures and tests | `docs`, `fixtures`, test files | They are development/support artifacts and were not proven unreachable. | Validation and deployment coverage. |

## D. Core Architecture

- **Builder entry point:** `apps/builder/app/routes/_ui.(builder).tsx` loads and authorizes a project, then mounts the Builder client UI.
- **Canvas entry point:** `apps/builder/app/routes/_canvas.canvas.tsx` lazy-loads `canvas/index.client` for rendered website content and visual editing.
- **Component registry:** SDK component packages (`sdk-components-react`, Radix and registry packages) provide the supported components and property definitions.
- **Component tree storage:** Nanostores in `shared/sync/data-stores.ts` hold pages, instances, props, assets and style selections. Instance utilities mutate the tree.
- **Project data:** synchronized state includes pages, instances, props, assets/folders, data sources/resources, breakpoints, style sources/selections, styles and project settings. Existing marketplace metadata is retained solely for backward compatibility.
- **Styles and responsiveness:** `css-engine`/`css-data`, breakpoint stores, style-source selections and the Builder style panel serialize responsive and pseudo-state rules.
- **State synchronization:** `shared/sync/sync-client.ts` selects singleplayer/multiplayer transport and applies loaded data to the stores.
- **Save/load:** project loading is performed through the Builder loader and `loadBuilderData`; mutations flow through the sync client and runtime mutation APIs.
- **Preview and publishing:** canvas preview uses the same renderer; `project-build` and `services/api-build.server.ts` generate runtime/build data, while project Publish settings control output options.

## E. Modified Files

- `builder/sidebar-left/sidebar-left.tsx` and `types.ts`: removed the Marketplace tab and its panel type.
- `builder/features/marketplace/*`: removed the isolated browsing, overview, detail, template-list and story UI.
- `builder/features/pages/page-settings/page-settings.tsx`: removed Marketplace listing controls.
- `builder/features/pages/page-settings/section-marketplace.tsx`: removed the page listing configuration UI.
- `builder/features/pages/template-settings.tsx`: removed the obsolete Marketplace-section option.

## F. Tests

| Command | Result |
|---|---|
| `./node_modules/.bin/tsc --noEmit --emitDeclarationOnly false -p apps/builder/tsconfig.json` | Passed after the removal. |
| `git diff --check` | Passed. |
| `pnpm --filter=@webstudio-is/builder typecheck` | Not run: `pnpm` is unavailable in this environment. Equivalent locally installed TypeScript command passed. |
| `./node_modules/.bin/remix vite:build` | Blocked before compilation: workspace package `@webstudio-is/protocol` lacks a built entry point in this checkout. |
| Unit, integration and browser tests | Untested: the environment lacks the package-manager command needed to resolve/run workspace scripts. |

## G. Remaining Requirements

- Install pnpm 9.14.4 and build workspace prerequisites before running the complete build/test suite.
- Run Builder browser/E2E coverage against a configured database/auth environment to validate load, edit, drag/drop, save/reload, preview and publishing.
- Keep any future marketplace metadata removal behind a compatibility migration/import adapter.
- A custom JSON import/export adapter, generic CMS/action editor, alternative authentication, or deployment replacement was not added because none is necessary for this scoped cleanup.
