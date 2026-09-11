# WatchdogとRestart

プロセス監視では、「落ちた」と「生きているが固まった」を分けて考える必要があります。

## Restart policy

異常終了したプロセスを再起動する仕組みです。

```ini
[Service]
ExecStart=/opt/example/worker
Restart=on-failure
RestartSec=2
```

概念的には次の流れです。

```text
worker起動
   ↓
異常終了
   ↓
systemdが検出
   ↓
再起動
```

ただし、無限に再起動を繰り返す設計は危険です。短時間に何度も失敗する場合は、原因調査やfail-safeへ移る仕組みも必要です。

## Watchdog

プロセスは存在していても、deadlockや無限ループなどで応答不能になることがあります。

```text
PID exists
    |
    +-- 正常とは限らない
```

watchdogでは、プロセスが定期的に「正常に動いている」と通知します。

```text
Worker
  |- alive
  |- alive
  |- alive
  X  timeout
       |
       v
   restart / fail-safe
```

## 設計上の注意

- 生存確認と「機能が正常」の確認は同じではない
- 再起動後に状態をどう復旧するかを考える
- restart loopを防ぐ
- 安全側へ倒す条件を決める
- ログやクラッシュ情報を残してから再起動する

watchdogやrestartは、単に「落ちたら起こす」機能ではなく、障害時にシステムをどう安全な状態へ戻すかという設計の一部です。
