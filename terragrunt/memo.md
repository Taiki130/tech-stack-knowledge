## required_versionsについて

- terragruntで直接terragrunt.hclのsourceにmoduleを使うときに、`Duplicate required providers configuration`が出る。
- 一般的にterraformでは、module側にもrequired_providersを入れる必要がある
- sourceにenvironmentのdirを指定し、そのdirの中でmoduleを使えば問題ないかもしれない
