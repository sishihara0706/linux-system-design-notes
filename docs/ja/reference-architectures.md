# 参考アーキテクチャ

このノートでは、公開されているOSSや公開設計資料を参考にして、Linux上の堅牢なシステム設計を学びます。

ここで扱う内容は、特定企業の内部システムを推測・再現するものではありません。

## Chromium

Chromiumは、ブラウザ本体、Rendererなどを複数プロセスへ分離する代表的なソフトウェアです。

学べるポイント:

- プロセス境界による障害分離
- IPCを使ったコンポーネント間通信
- UIと実処理の分離
- sandboxなどを利用した権限制御

```text
Browser process
      |
      +-- Renderer process
      +-- Renderer process
      `-- Other process
```

「1コンポーネントのクラッシュを全体へ波及させない」という考え方が重要です。

## PostgreSQL

PostgreSQLは、複数プロセスと共有メモリを使う成熟したサーバソフトウェアです。

学べるポイント:

- 複数プロセスの協調
- shared memory
- synchronization
- プロセスのライフサイクル管理

## systemd

systemdは、Linux上で多数のサービスを管理するSupervisorの代表例です。

学べるポイント:

- process supervision
- restart policy
- dependency management
- watchdog
- cgroups
- logging

## Linux containers

コンテナは単一のカーネル機能ではなく、複数のLinux機能を組み合わせたものです。

```text
namespaces -> isolation
cgroups    -> resource control
mount/rootfs -> filesystem view
capabilities/seccomp -> privilege restriction
```

## 何を真似するか

大規模OSSの構成そのものをコピーするのではなく、次のような設計原則を取り出して学びます。

- 障害をどこで分離するか
- 誰がプロセスのライフサイクルを管理するか
- データ量に応じてIPCをどう選択するか
- ハングやクラッシュをどう検出するか
- OSが提供する機能に何を任せるか
- 観測可能性をどう確保するか

重要なのは「有名なシステムと同じ技術を使うこと」ではなく、**なぜその技術が必要になったのかを理解すること**です。
