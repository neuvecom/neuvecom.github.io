# neuvecom.github.io
GithubPagesのテスト

- [neuvecom.github.io](https://neuvecom.github.io/)
- [リポジトリ](https://github.com/neuvecom/neuvecom.github.io.git)

## 概要
- Github Pagesは、Jekyllでビルドしている
- そこにmdbookを組み入れる

## mdBook
- [mdBookをGitHub Actionsでビルド・デプロイ(CI/CD) #Docker - Qiita](https://qiita.com/pyama2000/items/9d87d5dc2991d5f3c3e5)
  - mdBook向けに書かれたMarkdownファイル群をGitHubにプッシュ
  - GitHub ActionsでCI/CD
  - GitHub Pagesにデプロイ
```
mdbook2pages
├── book/
├── book.toml
├── docker-compose.yml
├── Dockerfile
├── README.md
└── src/
   ├── introduction.md
   ├── production/
   │  ├── mdbook2pages.md
   │  ├── moses.md
   │  ├── musa.md
   │  ├── spotif_api.md
   │  ├── tex2pdf.md
   │  └── tof.md
   └── SUMMARY.md
```
- ローカルまたはDockerでRust + mdBookを実行できる環境を構築
- Docker
  ```Dockerfile
  FROM rust:latest
  RUN cargo install mdbook --vers "^0.3.5"
  EXPOSE 3000
  WORKDIR /mdbook
  ```
  - `cargo install`でmdbookをインストールしてますが、
  - `--vers "^0.3.5"` インストールするバージョンを0.3.5から0.3.xまでに指定
  ```yaml:docker-compose.yml
  version: "3"
  services:
    mdbook:
      build: . 
      volumes:
        - ./:/mdbook
      ports:
        - 3000:3000
      container_name: mdbook2pages_mdbook
  ```
  - `docker-compose up --build` Dockerfileをビルド
- mdBook
  - プロジェクトの初期化
    - `mdbook init`
    - `mdbook build`
    - `mdbook serve -p 3001 --open` 
- GitHub Actionsとの連携
- GitHub Pages用のリポジトリを作成
  - `<USER_NAME>.github.io`というリポジトリを作成
  - 適当にindex.html
  - https://neuvecom.github.io/
- ユーザ用のサイトは1つしか作成できません
- プロジェクトごとにもGitHub Pagesを作成する場合は以下を実施
  - gh-pagesブランチにHTML、CSS、JavaScript
  - masterブランチの/docsディレクトリを配置
  - `https://<USER_NAME>.github.io/<REPOSITORY_NAME>`
- 秘密鍵・公開鍵の作成
```bash
ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages -N ""
```
- -CオプションでGitに登録しているメールアドレスをコメントとして渡し
- -Nオプションでパスフレーズを空白
- -fオプションによってpg-pagesというファイル名でキーペアを作成
- gh-pages.pub
  - GitHub Pages用のリポジトリ(USER_NAME.github.io)
  - Settings > Deploy keys
  - ACTIONS_DEPLOY_KEY
  - Allow write accessにチェック
- gh-pages
  - mdBookでビルドするリポジトリ