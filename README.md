# Monorepo Setup with pnpm, React, and Vite

This guide provides a step-by-step walkthrough for setting up a monorepo using pnpm, organizing multiple React applications with Vite, and managing shared components across applications.

This guide outlines setting up a monorepo using pnpm, organizing React applications with Vite, and configuring dependencies between apps.

## Step 1: Set Up Monorepo Directory Structure

1. **Create a Monorepo Directory**

- Create a main directory for the monorepo (e.g., monorepo) and navigate into it:

  ```bash
      mkdir monorepo && cd monorepo
  ```

2.  **Initialize the Monorepo with pnpm**

- Run pnpm init to create a root package.json file in the monorepo directory:

```bash
pnpm init
```

3.** Configure pnpm Workspace**

- Create a pnpm-workspace.yaml file at the root to define the workspace structure and manage shared dependencies across apps:

```yaml
# pnpm-workspace.yaml
packages:
  - "apps/*"
  - "packages/*"
```

## Step 2: Create Applications and Libraries

1. Set Up Applications Directory

- Create a directory called apps to hold application code, then create a new React app called host inside apps using Vite:

```bash
mkdir apps
cd apps
pnpm create vite host --template react
```

2. Set Up Packages Directory

- Create a directory called packages to hold shared libraries or components, then create a React library called lib:

```bash
mkdir ../packages
cd ../packages
pnpm create vite lib --template react
```

3. Update pnpm-workspace.yaml

-Make sure pnpm-workspace.yaml includes both apps/host and packages/lib:

```yaml
packages:
  - "apps/host"
  - "packages/lib"
```

## Step 3: Configure lib as a Reusable Component Library

1. Organize Reusable Components in lib

- In the lib directory, structure your reusable components (e.g., in a src/components folder).
- Example folder structure for lib:

```bash
packages/lib/
├── src/
│   └── components/
│       └── index.ts
│       └── MyComponent.jsx
├── index.ts
└── package.json
```

2. Export Components from lib

-In packages/lib/src/components/index.ts, export all reusable components:

```typescript
export * from "./MyComponent";
```

-In packages/lib/index.ts, export the directory containing the reusable components:

```typescript
export * from "./src/components";
```

<!--
3. Update lib package.json to support local sharing

- Ensure packages/lib/package.json has a name and export path:

```json
{
  "name": "lib",
  "version": "1.0.0",
  "main": "src/index.js",
  "type": "module",
  "dependencies": {}
}
``` -->

# Step 4: Link lib to host

1. Add lib as a Dependency in host

- In apps/host/package.json, add lib as a dependency:

```json
{
  "dependencies": {
    "lib": "workspace:*"
  }
}
```

2. Install Dependencies

- At the root of the monorepo (monorepo), run pnpm install to link lib to host:

```bash
Copy code
pnpm install
```

# Step 5: Use Shared Components from lib in host

1. Import Components in host

- In apps/host, import and use any exported component from lib. For example:

  ```javascript
  // Inside apps/host/src/App.jsx
  import { MyComponent } from "lib";
  function App() {
    return (
      <div>
        <h1>Host App</h1>
        <MyComponent />
      </div>
    );
  }
  export default App;
  ```

2. Start the Application

- Start the host app to verify the shared components render correctly:

```bash
cd apps/host
pnpm dev
```

# Step 6: Additional Tips

- Automatic Linking: With pnpm, workspace packages are automatically linked. If you make changes in lib, these will reflect in host on rebuild or reload.
- Testing Individual Packages: Run pnpm dev in each app or library directory to work on individual parts of the monorepo.
