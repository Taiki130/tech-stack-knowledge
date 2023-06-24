- Dockerのベースイメージにはdistrolessを使用する
    - パッケージマネージャ、シェルなどが入ってないためセキュリティ脆弱性が最小限、サイズが軽量
        - そのため、ビルドができないが、マルチステージビルドで解決できる
- Quay.ioでクラウドにコンテナレジストリを構築できる
- RailsをDockerで起動する際は、server.pidを削除するため、entrypoint.shを実行する
    - https://github.com/docker/awesome-compose/tree/master/official-documentation-samples/rails/

