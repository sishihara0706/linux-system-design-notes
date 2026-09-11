# epollとイベント駆動

`epoll` は、多数のファイルディスクリプタを効率よく監視するためのLinux APIです。

基本の流れは次のとおりです。

```text
epoll_create1()
    ↓
epoll_ctl() でfdを登録
    ↓
epoll_wait()
    ↓
readyになったfdを処理
    ↓
繰り返し
```

## 監視できるもの

`epoll` では、socketだけでなく次のようなfdも同じイベントループに統合できます。

- socket
- pipe
- eventfd
- timerfd
- signalfd

```text
              epoll_wait()
                   |
      +------------+------------+
      |            |            |
   socket       timerfd      eventfd
```

## select / pollとの違い

`select` や `poll` では、待つたびに監視対象の集合を渡して確認します。

`epoll` では監視対象をカーネル側へ登録し、`epoll_wait()` でreadyになったものを受け取ります。

多数のfdを扱うサーバやイベントループで特に有効です。

## よく使うイベント

- `EPOLLIN`: 読み込み可能
- `EPOLLOUT`: 書き込み可能
- `EPOLLERR`: エラー
- `EPOLLHUP`: 接続終了など
- `EPOLLET`: edge-triggered mode

最初はlevel-triggeredの基本形を理解してから `EPOLLET` を学ぶのがおすすめです。
