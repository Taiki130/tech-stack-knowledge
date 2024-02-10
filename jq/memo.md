## jqでフィルタに変数使うには？

https://github.com/googleapis/google-cloud-python/blob/614f9fd1a055c76f691b1d7811388c91c48d90da/scripts/updatechangelog.sh#L9

```bash
jq -r --arg package_location "$package_location" '.[$package_location]'
```
