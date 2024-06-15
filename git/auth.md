GitHub Apps から発行した token で HTTPS アクセスを行う場合 Basic 認証の ID に x-access-token、password に token を指定しておく必要がある。

> Installations with permissions on contents of a repository, can use their installation access tokens to authenticate for Git access. Use the installation access token as the HTTP password

[Authenticating with GitHub Apps - GitHub Docs](https://docs.github.com/en/developers/apps/building-github-apps/authenticating-with-github-apps#http-based-git-access-by-an-installation)

```
git config --global url."https://x-access-token:$GITHUB_TOKEN@github.com/".insteadOf https://github.com/
```

普段 PAT 等を利用する場合は ID に token を指定し、 password はx-oauth-basic、もしくは省略できる。

> you can simply use an OAuth token for the username and either a blank password or the string x-oauth-basic when cloning a repository.

[Easier builds and deployments using Git over HTTPS and OAuth | The GitHub Blog](https://github.blog/2012-09-21-easier-builds-and-deployments-using-git-over-https-and-oauth/#using-oauth-with-git)

```
git config --global url."https://$GITHUB_TOKEN:x-oauth-basic@github.com/".insteadOf https://github.com/
# or ?
git config --global url."https://$GITHUB_TOKEN@github.com/".insteadOf https://github.com/
```
