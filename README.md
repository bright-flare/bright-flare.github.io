<div align="center">

   # bright-flare blog

</div>

<br>

이 저장소는 [Jekyll](https://jekyllrb.com/)과 [Chirpy 테마](https://github.com/cotes2020/jekyll-theme-chirpy)를 기반으로 한 개인 블로그입니다.

<br>

## 🛠️ Project settings

<br>

### 1. Git clone

```sh
git clone https://github.com/bright-flare/bright-flare.github.io.git
```

### 2. Ruby 및 Bundler 설치

**Homebrew**로 Ruby 설치 (예시: rbenv 사용)

```sh
brew install rbenv
rbenv install 3.4.4
rbenv global 3.4.4
eval "$(rbenv init -)"
```

Bundler 설치
```sh
gem install bundler
```

### 3. 의존성 설치

```sh
bundle install
```

### 4. 로컬 서버 실행

```sh
bundle exec jekyll serve
```

- 실행 후, 브라우저에서 [http://localhost:4000](http://localhost:4000) 접속

### 5. (선택) 언어/테마 등 추가 설정

- `_config.yml` 파일에서 사이트 정보, 언어, 테마 등을 원하는 대로 수정할 수 있습니다.

### 6. 로컬 빌드/검증 (GitHub Actions 동일)

```sh
JEKYLL_ENV=production bundle exec jekyll b -d _site
bundle exec htmlproofer _site --disable-external --ignore-urls "/^http:\/\/127.0.0.1/,/^http:\/\/0.0.0.0/,/^http:\/\/localhost/"
```

---

<br>

## 📁 주요 폴더 구조

- `_posts/` : 블로그 포스트 파일
- `_data/` : 사이트 데이터 (저자, 언어, 미디어 등)
- `_includes/` : 재사용 가능한 HTML 조각
- `_layouts/` : 레이아웃 템플릿
- `_sass/` : 스타일(SCSS) 파일
- `assets/` : 이미지, JS, CSS 등 정적 파일

<br>

## 📝 블로그 포스트 작성법

1. `_posts/` 폴더에 `YYYY-MM-DD-제목.md` 형식으로 파일 생성
2. 파일 상단에 YAML Front Matter 추가
   ```yaml
   ---
   title: "포스트 제목"
   date: YYYY-MM-DD HH:MM:SS +0900
   categories: [카테고리]
   tags: [태그1, 태그2]
   ---
   ```
3. 본문 작성

<br>

## 📚 참고 자료

- [Jekyll 공식 문서](https://jekyllrb.com/docs/)
- [Chirpy 테마 문서](https://chirpy.cotes.page/)

---
