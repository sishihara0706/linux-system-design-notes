# cgroupsとnamespaces

Linuxコンテナや堅牢なプロセス運用を理解するうえで、`namespaces` と `cgroups` は重要なカーネル機能です。

## namespaces

namespaceは、プロセスから見える世界を分離します。

代表例:

- PID namespace: プロセスID空間
- mount namespace: マウントポイント
- network namespace: ネットワークスタック
- UTS namespace: hostnameなど
- IPC namespace: System V IPC / POSIX message queue
- user namespace: UID/GID

概念的には、同じLinuxカーネル上でもプロセスごとに異なる環境を見せられます。

```text
Linux kernel
   |
   +-- namespace A -> process group A
   `-- namespace B -> process group B
```

## cgroups

cgroupsは、プロセス群が使えるリソースを管理・制限します。

例:

- CPU
- memory
- I/O
- process数

```text
Worker group
 |- CPU: limited
 |- Memory: limited
 `- PIDs: limited
```

## namespacesとcgroupsの違い

簡単に言えば、

- namespace = **何が見えるかを分ける**
- cgroups = **どれだけ使えるかを制御する**

という役割です。

## systemdとの関係

現代のsystemdはサービスをcgroup単位で管理します。そのため、プロセス単体だけでなく、その子プロセスを含むグループ全体を管理しやすくなっています。

例えば、特定サービスにメモリ上限を設定すると、バグによるメモリ暴走がOS全体へ波及するのを抑えられる場合があります。

コンテナはこれらの機能を組み合わせて成立する抽象化であり、単一の「container syscall」が存在するわけではありません。
