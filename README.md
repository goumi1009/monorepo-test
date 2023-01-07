# yarn berry workspace로 monorepo 구성 해보기

지금 사용하고 있는게 vue이기 때문에 vue app으로 진행했습니다.
## 1. 모노레포 폴더 생성 및 yarn 설정

```
//yarn이 없다면 있다면 생략
npm install -g yarn

// 폴더 생성
mkdir monorepo-test
cd monorepo-test

// yarn 버전 확인 및 설정
yarn -v
yarn set version berry 
yarn -v
```

## 2. yarn workspace 생성

```
yarn init -w
```

## 3. 서비스와 공통 모듈구분 위해 apps, packages 폴더 구분하기

apps 폴더 생성 및 package.json workspaces에 apps추가하기
```
mkdir apps
cd apps
```
package.json 수정
<img width="311" alt="image" src="https://user-images.githubusercontent.com/40739041/211144010-c6cbe8e9-043f-451c-ba05-b38b61984968.png">


## 4. apps에 서비스 추가 (vue 앱 생성)

```
yarn create vue@3
```
<img width="571" alt="image" src="https://user-images.githubusercontent.com/40739041/211144050-3c00eee7-1179-4813-9763-1dd76d1912a0.png">
생성한 package.json name 변경
<img width="579" alt="image" src="https://user-images.githubusercontent.com/40739041/211144105-9d341d58-5f3c-4d34-9a79-19d56ab7d6d7.png">


## 5. 생성한 앱 root에서 실행 해보기 

```
// root(monorepo-test)로 가기
cd .. 

// 상태 갱신
yarn 

// 앱 실행 확인
yarn workspace @pikachu/eevee run dev
```

### TIP. workspace 활용하기 
```
// 특정 workspace에 정의된 스크립트 실행하기
yarn workspace <workspace_name> <commend>

// 특정 workspace에 의존성 추가하기
yarn workspace <workspace_name> add <packge_name>

// worksapce 의존 관계 확인
yarn workspace list

// 루트 프로젝트에 의존성 추가
yarn add <package_name> -w

```

## 6. yarn PnP 사용을 위한 vscode extension 설치 설정 추가하기

- 이미 추가 되어 있다면 skip
- TIP `.vscode/extensions.json`에 추가해두면 프로젝트에 필요한 확장을 vscode가 알려준다.
```json
{
  "recommendations": ["arcanis.vscode-zipfs"]
}
```

## 7. 공통 패키지 만들기

1. packages 폴더에 lib 폴더 생성

```
// lib 폴더 생성
cd packages
mkdir lib
cd lib

// package.json 파일 생성
yarn init
```

2. `packages/lib/package.json` 수정
```json
{
  "name": "@pokemon/lib",
  "version": "0.0.0",
  "private": true,
  "main": "./src/index.ts",
  "dependencies": {
    "typescript": "^4.9.3"
  }
}
```

3. `packages/lib/tsconfig.json` 파일 생성 후 설정값 넣기
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

4. 공통모듈로 사용할 코드 만들기 `packages/lib/src/index.ts` 생성하고 간단한 코드 넣기
```javascript
export const helloWorld = () => {
  console.log("hello world from lib");
  return "hello world from lib";
};
```


## 8. app에서 공통모듈 의존 하기

1. `apps/eevee`에 `packages/lib` 의존성 주입
```
// root로 돌아가기
cd ...
yarn workspace @pokemon/eevee add @pikachu/lib
```
2. `apps/eevee/package.json` `dependencieds`를 확인해보면 의존성이 추가된 것을 확인할 수 있다.
## 9. `packages/lib` 사용해 보기

`apps/eevee/src/components/HelloWorld.vue`에서 helloWorld 호출 해보기
```javascript
<script setup lang="ts">
import { helloWorld } from "@pokemon/lib";

defineProps<{
  msg: string;
}>();
</script>

<template>
  <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
    <h2>{{ helloWorld() }}</h2>
    <h3>
      You’ve successfully created a project with
      <a href="https://vitejs.dev/" target="_blank" rel="noopener">Vite</a> +
      <a href="https://vuejs.org/" target="_blank" rel="noopener">Vue 3</a>.
      What's next?
    </h3>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
```
`apps/eevee` 실행
```
yarn workspace @pokemon/eevee run dev
```
![image](https://user-images.githubusercontent.com/40739041/211144353-9dde562e-1c64-4746-b8ef-288796151643.png)
위 이미지처럼 파란 박스 부분에 hello from lib이 출력 됐다면 성공!

## TS 오류 처리 방법
- 앱을 추가하고 ts관련 파일에서 오류가 발생하고 있을 겁니다. (`apps/eevee/tsconfig.json`, `apps/eevee/main.ts`등)
- yarn berry가 npm과 모듈을 불러오는 방식이 다르기 때문에 생기는 문제 입니다.
- 아래 명령어를 root(monorepo-test)에서 실행 하세요.(순서도 지켜주세요.)
```
yarn add -D typescript
yarn dlx @yarnpkg/sdks vscode
```
혹시 여전히 오류가 난다면, 
1. `cmd+shift+p`
2. `TypeScript: Restart TS server.`
3. `Enter`

vue파일에서 module 관련 오류가 있는데 이는 방법을 찾게되면 바로 업데이트 하도록 하겠습니다.

## 10. package간 설정 공유

설정 관련 공부를 마치는 대로 추가하겠습니다.


# clone 실행 방법
```
// yarn 버전 확인
yarn -v 

// yarn 버전 맞추기 @3
yarn set version berry

// workspace 실행해보기
yarn workspace @pokemon/eevee run dev
```
