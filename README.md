Repo that reproduces error when importing files from libraries in expo.

## Repro steps

Init
```bash
pnpm create create-nx-workspace@latest expo-with-lib --workspaceType=integrated --nxCloud=skip --preset=apps --defaultBase=main
```

Install expo
```bash
pnpm exec nx add @nx/expo
```

Fix .npmrc
```bash
strict-peer-dependencies=false
auto-install-peers=true
node-linker=hoisted
```

Reinstall dependencies
```bash
pnpm i
```

Generate app
```bash
pnpm exec nx generate @nx/expo:application --name=expo-app --directory=apps/expo-app --e2eTestRunner=playwright --projectNameAndRootFormat=as-provided --no-interactive
```

Generate lib
```bash
pnpm exec nx generate @nx/expo:library --name=expo-lib --directory=libs/expo-lib --projectNameAndRootFormat=as-provided --no-interactive
```

Add dummy component and export

**libs/expo-lib/src/Test.tsx**
```tsx
import { View, Text } from "react-native"

export const Test = () => {
  return <View><Text>Hello from lib</Text></View>
}
```

**libs/expo-lib/src/index.ts**
```tsx
export * from './Test';
```

Use lib in App.tsx

**apps/expo-app/src/app/App.tsx
```tsx
import { Test } from '@expo-with-lib/expo-lib';

return (
  //...
    <Test/>
)
```

Bundler fails with error:
```text
Web Bundling failed 1175ms (C:\projects\temp\expo-with-lib\apps\expo-app\index.js)
error: Error: Cannot resolve @expo-with-lib/expo-lib
    at firstResolver (C:\projects\temp\expo-with-lib\node_modules\@nx\expo\plugins\metro-resolver.js:33:15)
    at firstResolver (C:\projects\temp\expo-with-lib\node_modules\expo\node_modules\@expo\cli\src\start\server\metro\withMetroResolvers.ts:108:16)
    at resolveRequest (C:\projects\temp\expo-with-lib\node_modules\expo\node_modules\@expo\cli\src\start\server\metro\withMetroResolvers.ts:137:16)
    at Object.resolve (C:\projects\temp\expo-with-lib\node_modules\metro-resolver\src\resolve.js:32:12)
    at ModuleResolver.resolveDependency (C:\projects\temp\expo-with-lib\node_modules\metro\src\node-haste\DependencyGraph\ModuleResolution.js:73:31)
    at DependencyGraph.resolveDependency (C:\projects\temp\expo-with-lib\node_modules\metro\src\node-haste\DependencyGraph.js:231:43)
    at C:\projects\temp\expo-with-lib\node_modules\metro\src\lib\transformHelpers.js:156:21
    at resolveDependencies (C:\projects\temp\expo-with-lib\node_modules\metro\src\DeltaBundler\buildSubgraph.js:42:25)
    at visit (C:\projects\temp\expo-with-lib\node_modules\metro\src\DeltaBundler\buildSubgraph.js:83:30)
    at processTicksAndRejections (node:internal/process/task_queues:95:5)
```
