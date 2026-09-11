# Linux System Design Notes（日本語）

Linux上で動作する堅牢なシステムソフトウェア、マルチプロセス構成、IPC、イベント駆動、プロセス監視を学ぶための個人学習ノートです。

## 注意事項

このリポジトリは、公開されているLinux API、OSS、一般的なソフトウェア設計パターンをもとにした学習資料です。

**特定企業の製品、社内アーキテクチャ、ソースコード、機密情報、独自実装を説明するものではありません。**

目的は、公開情報を参考にしながら、モダンで堅牢なLinuxシステム設計の考え方を理解することです。

## トピック

- マルチプロセスアーキテクチャ
- IPCと共有メモリ
- `select` / `poll` / `epoll`
- `eventfd` / `timerfd` / `signalfd`
- `systemd` / restart policy / watchdog
- namespaces / cgroups
- プロセスのライフサイクル管理と障害分離
- 公開OSSを参考にしたアーキテクチャ例

## おすすめの読む順番

1. [マルチプロセス構成](docs/ja/multiprocess-architecture.md)
2. [IPC](docs/ja/ipc.md)
3. [epollとイベント駆動](docs/ja/epoll.md)
4. [systemdとプロセス監視](docs/ja/systemd.md)
5. [watchdogとrestart](docs/ja/watchdog-and-restart.md)
6. [cgroupsとnamespaces](docs/ja/cgroups-and-namespaces.md)
7. [参考アーキテクチャ](docs/ja/reference-architectures.md)

## 基本イメージ

```text
                 Supervisor
                     |
        +------------+------------+
        |            |            |
     Worker A     Worker B     Worker C
        |            |
        +--- IPC ----+
             |
      shared memory

イベント監視:
    epoll
      |- socket
      |- eventfd
      |- timerfd
      `- signalfd

プロセス管理:
    systemd
      |- restart
      |- watchdog
      `- cgroups
```

## 設計するときの問い

1. なぜここをプロセス境界にするのか？
2. Workerが1つ落ちたとき、全体はどうなるか？
3. 各プロセスの起動・停止を誰が管理するか？
4. どのIPC手段を使うべきか？
5. 制御メッセージと大容量データをどう分けるか？
6. ハングしたプロセスをどう検出するか？
7. CPU・メモリの暴走をどう抑えるか？
8. 安全に終了するにはどうするか？
9. ログや障害をどう観測するか？
10. アプリで持つ責務とOSに任せる責務をどう分けるか？

## 目標

LinuxカーネルAPIとUnix系の設計原則を組み合わせて、小規模でも堅牢なシステムソフトウェアを設計できるようになることを目標にしています。

[English README](README.md)
