Kustomize Built-in Transformersの詳細解説
KustomizeはKubernetesの設定をカスタマイズするためのツールで、様々なビルトイントランスフォーマー（内蔵の変換機能）を提供しています。以下では、このドキュメントページに記載されている各ビルトイントランスフォーマーについて、詳細に説明します。

## NamespaceTransformer
目的:
NamespaceTransformerは、リソースのnamespaceフィールドを設定または更新するためのトランスフォーマーです。

利用方法:

unsetOnly: デフォルトではfalse。trueに設定すると、現在未設定（空文字列または存在しない）のnamespaceフィールドのみを設定します。
setRoleBindingSubjects: RoleBindingおよびClusterRoleBindingオブジェクト内のsubjects[].namespaceフィールドの取り扱いを制御します。
defaultOnly: デフォルト。名前が「default」のsubjectsのnamespaceを更新します。
allServiceAccounts: kindがServiceAccountのすべてのsubjectsのnamespaceを更新します。
none: subjectsを更新しません。
例:

```yaml

apiVersion: builtin
kind: NamespaceTransformer
metadata:
  name: example-namespace-transformer
namespace: new-namespace
unsetOnly: true
setRoleBindingSubjects: allServiceAccounts
```

## PatchTransformer
目的:
PatchTransformerは、リソースに対してパッチを適用するためのトランスフォーマーです。

利用方法:

patches: kustomization.yamlのフィールド名。
path: パッチファイルの場所を指定します。
target: グループ、バージョン、種別、名前、namespace、ラベルセレクター、およびアノテーションセレクターでリソースを選択します。
例:

```yaml

patches:
- path: patch.yaml
  target:
    group: apps
    version: v1
    kind: Deployment
    name: deploy.*
    labelSelector: "env=dev"
    annotationSelector: "zone=west"
- patch: |-
    - op: replace
      path: /spec/template/spec/containers/0/image
      value: nginx:latest
  target:
    kind: Deployment
    name: .*-deploy
    labelSelector: "env=dev"
```
## PrefixTransformer
目的:
PrefixTransformerは、リソース名にプレフィックスを追加するためのトランスフォーマーです。

利用方法:

prefix: 追加するプレフィックス文字列を指定します。
fieldSpecs: プレフィックスを適用するフィールドとリソースの種類を指定します。
例:

```yaml
apiVersion: builtin
kind: PrefixTransformer
metadata:
  name: example-prefix-transformer
prefix: my-prefix-
fieldSpecs:
- path: metadata/name
  kind: Deployment
SuffixTransformer
目的:
SuffixTransformerは、リソース名にサフィックスを追加するためのトランスフォーマーです。

利用方法:

suffix: 追加するサフィックス文字列を指定します。
fieldSpecs: サフィックスを適用するフィールドとリソースの種類を指定します。
例:

```yaml

apiVersion: builtin
kind: SuffixTransformer
metadata:
  name: example-suffix-transformer
suffix: -suffix
fieldSpecs:
- path: metadata/name
  kind: Deployment
```

## LabelTransformer
目的:
LabelTransformerは、リソースにラベルを追加または変更するためのトランスフォーマーです。

利用方法:

labels: 追加または変更するラベルのキーと値のペアを指定します。
fieldSpecs: ラベルを適用するフィールドとリソースの種類を指定します。
例:

```yaml
apiVersion: builtin
kind: LabelTransformer
metadata:
  name: example-label-transformer
labels:
  app: my-app
  env: production
fieldSpecs:
- path: metadata/labels
  kind: Deployment
```
## AnnotationsTransformer

目的:
AnnotationsTransformerは、リソースにアノテーションを追加または変更するためのトランスフォーマーです。

利用方法:

annotations: 追加または変更するアノテーションのキーと値のペアを指定します。
fieldSpecs: アノテーションを適用するフィールドとリソースの種類を指定します。
例:

```yaml
apiVersion: builtin
kind: AnnotationsTransformer
metadata:
  name: example-annotations-transformer
annotations:
  description: "This is an example annotation"
  owner: "admin"
fieldSpecs:
- path: metadata/annotations
  kind: Deployment
```

## ImageTagTransformer
目的:
ImageTagTransformerは、コンテナ仕様内のイメージタグを更新するためのトランスフォーマーです。

利用方法:

imageTag: イメージ名と新しいタグを指定します。
fieldSpecs: イメージタグを更新するフィールドとリソースの種類を指定します。
例:

```yaml
apiVersion: builtin
kind: ImageTagTransformer
metadata:
  name: example-image-tag-transformer
imageTag:
  name: nginx
  newTag: latest
fieldSpecs:
- path: spec/template/spec/containers/0/image
  kind: Deployment
```

## ReplicasTransformer
目的:
ReplicasTransformerは、DeploymentおよびStatefulSetリソースのレプリカ数を設定するためのトランスフォーマーです。

利用方法:

replica: 新しいレプリカ数を指定します。
fieldSpecs: レプリカ数を設定するフィールドとリソースの種類を指定します。
例:

```yaml

apiVersion: builtin
kind: ReplicasTransformer
metadata:
  name: example-replicas-transformer
replica: 3
fieldSpecs:
- path: spec/replicas
  kind: Deployment
```
これらのビルトイントランスフォーマーを使用することで、Kubernetesリソースの設定を柔軟にカスタマイズすることができます。各トランスフォーマーは特定の目的に特化しており、必要に応じて適切なトランスフォーマーを選択し、kustomization.yamlファイルに定義することで、効率的な設定管理が可能になります。

## SecretGenerator
目的:
SecretGeneratorは、Kubernetesのシークレットリソースを生成するために使用されます。

パラメータ:

name: シークレットの名前を指定します。
commands: シークレットのデータを生成するためのコマンドを指定します。
files: シークレットのデータを含むファイルを指定します。
literals: シークレットのキーと値のペアを直接指定します。

```yaml
secretGenerator:
- name: my-secret
  literals:
  - username=admin
  - password=secret
  files:
  - config.json

```

## ConfigMapGenerator
目的:
ConfigMapGeneratorは、KubernetesのConfigMapリソースを生成するために使用されます。

パラメータ:

name: ConfigMapの名前を指定します。
commands: ConfigMapのデータを生成するためのコマンドを指定します。
files: ConfigMapのデータを含むファイルを指定します。
literals: ConfigMapのキーと値のペアを直接指定します。

```yaml
configMapGenerator:
- name: my-config
  literals:
  - key1=value1
  - key2=value2
  files:
  - config.properties
```
