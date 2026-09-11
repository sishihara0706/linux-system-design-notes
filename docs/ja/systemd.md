# systemdとプロセス監視

`systemd` はLinux上でサービスやプロセスの起動・停止・依存関係・監視などを管理する仕組みです。

## 基本イメージ

```text
             systemd
                |
      +---------+---------+
      |         |         |
    GUI      Worker A  Worker B
```

アプリケーション自身がすべてのプロセス管理を抱え込まず、OS側のサービスマネージャに任せられる部分があります。

## service unitの例

```ini
[Unit]
Description=Example Worker

[Service]
ExecStart=/opt/example/worker
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

## systemdに任せられること

- 起動・停止
- 起動順序や依存関係
- 異常終了時の再起動
- watchdog連携
- 標準出力・標準エラーのログ管理
- cgroupsによるプロセスグループ管理
- CPU・メモリなどのリソース制限

## なぜSupervisorとして便利なのか

GUI自身がWorkerを起動・監視すると、GUIが落ちたときにSupervisorまで失われる可能性があります。

```text
GUI
 |- Worker A
 `- Worker B
```

これをsystemd側へ寄せると、GUIとWorkerを独立して管理できます。

```text
systemd
 |- GUI
 |- Worker A
 `- Worker B
```

どこまでsystemdに任せ、どこからアプリケーション内部で管理するかは、システムの要件に応じて決めます。
