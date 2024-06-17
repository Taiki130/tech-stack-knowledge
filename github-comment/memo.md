```yaml
---
templates:
  diff_title: "## :white_check_mark: manifest diff ({{.Vars.app}})"
  diff_content: |
    {{template "link" .}}
    {{template "join_command" .}}
    Diff {{if eq .ExitCode 1}}found{{end}} :eyes:
    {{template "hidden_combined_output" .}}
  diff_content_for_too_long: |
    {{template "link" .}}
    {{template "join_command" .}}
    Diff {{if eq .ExitCode 0}}found{{else}}failed{{end}} :eyes:
    comment is too long
exec:
  diff:
    - when: ExitCode == 1
      template: |
        {{template "diff_title" .}}
        {{template "diff_content" .}}
      template_for_too_long: |
        {{template "diff_title" .}}
      embedded_var_names: [app]
hide:
  diff: 'Comment.HasMeta && Comment.Meta.TemplateKey == "diff" && Comment.Meta.Vars.app == Vars.app'
```


```bash
github-comment exec -k diff -pr ${{ github.event.pull_request.number }} --config .github/.github-comment.yaml -var 'app:${{ matrix.app }}' 
```

- Vars.xxx にして、 -var 'xxx:yyy' で変数を使える
- Comment.Meta.Varsに変数を埋めるには、embedded_var_namesキーを指定する

