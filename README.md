## yarn berry 버전 변경
```
yarn -v
yarn set version berry
yarn -v
```

## yarn workspace 생성
```
yarn init -w
```

## 서비스와 공통 모듈구분 위해 apps, packages 폴더 구분하기

apps 폴더 생성 및 package.json workspaces에 apps추가하기

## apps에 서비스 추가 (vue 앱 생성)
```
cd apps
npm init vue@latest
```

생성한 package.json name 변경 (예제에서는 @pikachu/web)

## 생성한 앱 root에서 실행 해보기 
```
cd ..
yarn 
yarn workspace @pikachu/web dev
```

## 설정 추가하기
.vscode/extensions.json 수정
```json
{
  "recommendations": ["arcanis.vscode-zipfs"]
}
```

## 공통 패키지 만들기
packages 폴더에 lib 폴더 생성

```
// lib 폴더 이동
cd packages/lib

// package.json 파일 생성
yarn init
```

lib package.json 수정
```json
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

lib tsconfig.json 파일 생성후 설정값 넣기
```json
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

index.ts 생성하고 간단한 코드 넣기
```javascript
export const helloWorld = () => {
  console.log("hello world from lib");
  return "hello world from lib";
};
```


## apps/pikachu에 lib의존 하기
```
cd ...
yarn workspace @pikachu/web add @pikachu/lib
```
apps/pikachu의 package.json 파일에 Dependencies으로 @pikachu/lib이 추가된걸 확인할 수 있다.