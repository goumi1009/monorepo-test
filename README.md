```
yarn -v
yarn set version berry
yarn -v

yarn init -w
```
apps 폴더 생성

```
cd apps
npm init vue@latest
```

생성한 package.json name 변경 (예제에서는 @pikachu/web)

```
cd ..
yarn 
yarn workspace @pikachu/web dev
```

.vscod/extensions.json 수정
```
{
  "recommendations": ["arcanis.vscode-zipfs"]
}
```

공통 패키지 만들기
```
// lib 폴더 이동
cd packages/lib

// package.json 파일 생성
yarn init
```

lib package.jso 수정
```
{
  "name": "@wanted/lib",
  "version": "1.0.0",
  "private": true,
  "main": "./src/index.ts",
  "dependencies": {
    "typescript": "^4.9.3"
  }
}
```

tsconfig.json 파일 생성후 설정값 넣기
```
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "compilerOptions": {
    "strict": true,
    "useUnknownInCatchVariables": true,
    "allowJs": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "newLine": "lf",
    "module": "ESNext",
    "moduleResolution": "node",
    "target": "ESNext",
    "lib": ["ESNext", "dom"],
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "baseUrl": "./src",
    "noEmit": false,
    "incremental": true,
    "resolveJsonModule": true,
    "paths": {}
  },
  "exclude": ["**/node_modules", "**/.*/", "./dist", "./coverage"],
  "include": ["**/*.ts", "**/*.js", "**/.cjs", "**/*.mjs", "**/*.json"]
}
```

