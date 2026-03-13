# HiClaw + CoPaw 南网场景深度结合方案

**作者**: Damon Li

## 1. 核心挑战：南网内网环境的“三座大山”

南方电网的 AI Agent 落地面临三大核心挑战：

1.  **资源受限**：内网服务器资源有限，无法像公有云一样无限扩展，对 Agent 的内存占用和启动速度要求苛刻。
2.  **安全隔离**：物理隔离或严格的网络策略，导致 Agent 无法访问公网，且对本地环境的访问需要严格管控。
3.  **国产化适配**：要求软硬件环境全面国产化，对 Agent 框架的语言、依赖和生态有特殊要求。

## 2. HiClaw + CoPaw：为南网量身定制的“龙虾组合”

HiClaw 的 Manager-Worker 架构与 CoPaw 的轻量化特性，为解决南网的“三座大山”提供了完美的解决方案。

### 2.1 HiClaw：内网的“AI 管家”与“安全网关”

HiClaw 在南网场景中扮演两个核心角色：

-   **AI 管家 (Manager)**：负责任务分解、Worker 调度、多 Agent 协作，将复杂的业务流程自动化。
-   **安全网关 (Higress AI Gateway)**：集中管理所有 Worker 的凭证，Worker 本身不持有任何真实密钥，从根本上解决了安全问题。

### 2.2 CoPaw：轻量、灵活、可控的“数字员工”

CoPaw 作为 Worker，为南网场景带来了三大优势：

| 特性 | 优势 | 南网场景价值 |
| :--- | :--- | :--- |
| **轻量级 (Python)** | 内存占用仅为 OpenClaw 的 1/5 (~150MB)，启动速度快 | 极大降低了内网服务器的资源压力，支持大规模部署“数字员工” |
| **本地模式** | 可直接访问宿主机的文件系统、浏览器和桌面应用 | 完美解决了内网隔离环境下需要操作本地资源（如：读取本地报表、操作内网 OA 系统）的痛点 |
| **可视化控制台** | 内置 Web 控制台，可管理频道、技能、定时任务、工作区文件 | 提供了可视化的运维管理界面，降低了内网运维的复杂度 |

## 3. 南网场景下的推荐部署架构

我们推荐采用“**HiClaw Manager (Docker) + CoPaw Worker (本地模式)**”的混合部署架构：

```mermaid
graph TD
    subgraph 内网服务器 (Docker 环境)
        A[HiClaw Manager] --> B{Tuwunel Matrix Server}
        A --> C{Higress AI Gateway}
        A --> D{MinIO 共享文件系统}
    end

    subgraph 内网办公机 (宿主机环境)
        E[CoPaw Worker 1 (本地模式)]
        F[CoPaw Worker 2 (本地模式)]
    end

    B -- Matrix 协议 --> E
    B -- Matrix 协议 --> F

    style A fill:#f9f,stroke:#333,stroke-width:2px
    style E fill:#bbf,stroke:#333,stroke-width:2px
    style F fill:#bbf,stroke:#333,stroke-width:2px
```

-   **HiClaw Manager** 运行在 Docker 容器中，负责统一管理和调度。
-   **CoPaw Worker** 以本地模式运行在内网的办公机上，可以直接访问本地文件、操作内网应用。
-   所有通信通过 **Matrix 协议** 进行，实现了安全、可靠、可审计的协作。

## 4. 结论与建议

HiClaw + CoPaw 的组合，为南网提供了一套**轻量、安全、可控**的 AI Agent 解决方案。

-   **轻量**：CoPaw 的低内存占用和快速启动，解决了内网资源受限的问题。
-   **安全**：HiClaw 的安全网关和 Matrix 协议，解决了内网安全隔离的问题。
-   **可控**：CoPaw 的本地模式和可视化控制台，解决了内网环境操作和运维的问题。

我们强烈建议南网采用此方案，作为其 AI Agent 平台建设的技术底座。

## 5. 参考文献

[1] 阿里龙虾组合来了：HiClaw + CoPaw，内存占用大幅降低. (2026, March 13). 阿里云开发者. https://mp.weixin.qq.com/s/JG4QiKsUys6j1rXL1VAuMw
[2] alibaba/hiclaw. (2026). GitHub. https://github.com/alibaba/hiclaw
[3] agentscope-ai/CoPaw. (2026). GitHub. https://github.com/agentscope-ai/CoPaw
