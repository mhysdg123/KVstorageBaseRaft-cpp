# KVstorageBaseRaft-cpp

基于 C++ 和 Raft 共识算法的分布式键值存储学习项目，包含领导者选举、日志复制、持久化、快照以及键值读写等模块。

## 项目组成

- **Raft 核心**：维护节点状态，通过 RPC 完成选举与日志复制。
- **键值服务**：协调 Raft 与状态机，处理客户端请求。
- **存储引擎**：使用跳表组织键值数据。
- **RPC 通信**：基于 Muduo 和 Protobuf 实现节点之间的通信。
- **客户端**：提供键值读写接口和调用示例。

## 构建环境

- Linux（示例使用 POSIX 进程与网络接口）
- 支持 C++20 的编译器
- CMake 3.22 或更高版本
- Protobuf 及 `protoc` 编译器
- Muduo 网络库
- Boost.Serialization

依赖库的头文件和库文件需要位于编译器可搜索的路径中。仓库包含生成的 Protobuf 文件；重新生成时，应使用与本机 Protobuf 库兼容的 `protoc` 版本。

## 编译

在项目根目录执行：

```bash
cmake -S . -B build
cmake --build build -j
```

可执行文件输出到 `bin/`，库文件输出到 `lib/`。

## 运行示例

在项目根目录打开一个终端，启动本机三个节点：

```bash
./bin/raftCoreRun -n 3 -f test.conf
```

该程序创建多个节点进程，并将本机节点地址写入指定配置文件。启动时会清空该配置文件，端口随机选择。

等待节点启动后，在另一个终端中进入同一项目目录，执行客户端：

```bash
./bin/callerMain
```

客户端示例读取当前目录下的 `test.conf`，循环写入并读取键 `x`。运行产生的配置和持久化文件位于运行目录；重复实验时应注意已有数据与节点进程的状态。

## 目录与文档

| 路径 | 内容 |
| --- | --- |
| `src/raftCore/` | Raft 核心与键值服务 |
| `src/raftClerk/` | 客户端 |
| `src/raftRpcPro/` | Raft 与键值服务的 Protobuf 定义及生成文件 |
| `src/rpc/` | RPC 框架 |
| `src/skipList/` | 跳表存储引擎 |
| `src/common/` | 配置、序列化和通用工具 |
| `src/fiber/` | 协程相关组件 |
| `example/` | Raft、RPC 和协程示例 |
| `test/` | 独立测试与实验代码 |

- [目录导览](docs/目录导览.md)
- [RPC 示例说明](example/rpcExample/rpc_example.md)
- [测试文件运行说明](test/测试文件运行说明.md)

## 学习与改进方向

建议先了解 C++ 并发、RPC、序列化以及 Raft 的基本概念，再结合代码跟踪选举、日志复制和状态机应用流程。

本项目用于学习与实验，协议正确性、故障恢复行为和性能需要结合测试验证。可继续完善网络分区与节点重启测试、请求去重、可观测性和性能评估。
