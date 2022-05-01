## ✏️ Vue의 설치

- 우선 Vue 설치를 돕는 Vue-cli를 사용하기 위해 node가 설치되어 있어야 한다. 없다면 LTS 버전을 OS에 맞게 설치한다.

- Vue-cli를 전역으로 설치한다.

```
npm install -g @vue/cli
```

- 설치를 확인하기 위해 다음을 시행한다.

```
vue --version
```

---

## ✏️ 프로젝트 세팅

- Vue 설치가 완료되면 프로젝트를 세팅해야 한다.
- Vue project는 아래 command로 생성 가능하다.

```
vue create project-name
```

커맨드 입력 시 `Please pick a preset` 창이 뜬다. 개중 단순히 js문법만 사용하고자 한다면 `Default ([Vue 3] babel, eslint)`를 입력해도 되지만, 보다 정밀한 세팅을 위해 `Manually select features`를 고른다.  
프로젝트에 필요한 특징들이 다양하게 나온다. 전부 있어도 괜찮기 때문에 a를 눌러 전부 선택하고, enter를 누른다.  
그 뒤 뜨는 문구를 보면서 맞는 세팅을 한다.

```
3.x
Use class-style component syntax? yes
Use Babel alongside TypeScript? Yes
Use history mode for router?
```

router의 설정 값 인데 history mode와 hash mode가 존재하며, 필요에 따라 선택한다.  
history mode : URL이 변경될 때 페이지를 다시 로드하기에 URL에 대해서 index.html로 전달해주는 서버 설정이 필요하다.
hash mode : URL 해시를 사용하여 전체 URL을 시뮬레이트하므로 URL이 변경될 때 페이지가 다시 로드 되지 않는다.

```
Pick a CSS pre-processor: Sass/SCSS
Pick a linter / formatter config:
```

보통 자주 사용하는 것은 ESLint + Prettier

```
Pick additional lint features:
```

Lint and fix on commit이 추천된다.

```
Pick a unit testing solution:
Jest
Mocha + Chai
```

TDD 도구의 일종을 선택한다.

```
Pick an E2E testing solution:
Cypress (Test in Chrome, Firefox, MS Edge, and Electron)
Nightwatch (WebDriver-based)
WebdriverIO (WebDriver/DevTools based)
```

E2E 테스트 도구를 선택한다.

```
Where do you prefer placing config for Babel, ESLint, etc.?
In dedicated config files
In package.json
```

설정 값들을 config 파일로 관리할지, package.json 에서 관리할지 고른다.
이 글에서는 package.json을 선택.

---

## ✏️ 실행 테스트

설치가 완료되었으면 잘 실행되는지 터미널 창에 다음을 입력하고, http://localhost:8080/ 으로 접근한다.

```
npm run serve
```

잘 실행되었다면 성공!
