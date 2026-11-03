# nv-uefi-unlocker 中文整合包

把 [physicsdolphin/nv-uefi-unlocker](https://github.com/physicsdolphin/nv-uefi-unlocker) **v2.1.9** 整理成可以直接上手的中文交付包：官方发布包原样入库、中文手册与操作教程、诊断脚本、完整源码，附哈希校验记录。

> 本仓库是**第三方中文整合**，不是上游官方项目。所有解锁功能的实现均来自上游，本仓库的增量只有中文文档与目录整理。

---

## 这个工具是做什么的

UEFI 预驱动层解锁器：在 NVIDIA 驱动加载之前介入，解开 GeForce 显卡被固件限制的 Tensor / 计算能力。

几个关键性质，决定了它和「刷 VBIOS」完全是两条路：

- **不刷固件、不动 VBIOS** —— 每次冷启动重放一次，断电即复原
- **易失性** —— 不留下持久修改，不存在「刷坏了变砖」
- **需要 U 盘启动** —— 运行在 UEFI 环境，不依赖操作系统
- **需要关闭 Secure Boot** —— 否则 loader 无法执行

支持 GTX 16 / RTX 20 / RTX 30 / RTX 40 系。逐卡兼容性见 `使用手册.html` 第 04 章。

---

## 先看哪个文件

| 顺序 | 文件 | 用途 |
|---|---|---|
| 1 | **`操作教程.html`** | 手把手 12 节，从解压这个包走到验证成功。每步固定「做什么 / 看到什么 / 不对怎么办」 |
| 2 | **`使用手册.html`** | 参考手册，11 章 + 4 附录。判断可行性、查兼容性、排故障时翻 |
| 3 | `快速上手.md` | 一页纸三步走，赶时间看这个 |
| 4 | `README.txt` | 纯文本目录说明（BOM + CRLF，记事本直接可读） |

两份 HTML 都是**单文件、离线可用**，双击用浏览器打开即可，深色/浅色主题自适应，可直接打印。

---

## 目录结构

```
.
├─ 操作教程.html            67 KB   线性操作教程（建议从这里开始）
├─ 使用手册.html           116 KB   参考手册，含 5 张手绘流程图
├─ 快速上手.md             3.2 KB   一页纸速查
├─ README.txt              3.8 KB   纯文本结构说明
├─ 00_checksums/           2 文件   官方 SHA256SUMS + 本地校验记录
├─ 01_release-v2.1.9/      9 文件   官方发布包原样（BOOTX64.EFI、制盘脚本、
│                                   assets、ssdiag），路径全 ASCII 便于制盘
├─ 02_diag/                2 文件   诊断与兼容性采集脚本
├─ 03_docs-zh/             4 文件   上游中文原文档（README / FAQ / 兼容性 / 部署）
└─ 04_source/            186 文件   上游源码（Rust），已剔除 .git
```

制盘务必在 `01_release-v2.1.9/` 内执行 —— 该路径全为 ASCII，可避开 PowerShell 5.1 在中文路径下的编码问题。

---

## 三条最容易踩的坑

写在最前面，因为照着上游 README 直接做很容易掉进去：

1. **`cargo efi` 编出来的不能解锁** —— 那是只读采样程序。要解锁必须用 `cargo efi-tx`（带 `--features tx`）。敲前者只会「什么都没发生」。
2. **Turing 的「门关了」是正常交棒状态** —— 解锁路径本来就主动重锁 PLM。套用 Ampere / Ada 的「门关即失效」判据会全程误判。
3. **U 盘里不要整目录拖进去** —— 必须把 `usbroot` 的**内容**放到 U 盘根目录，多套一层 loader 就找不到固件。`操作教程.html` 里有一张搬运关系图专门讲这个。

---

## 完整包下载

如果不想逐个文件 clone，可以直接下 Release 里的 `nv-uefi-unlocker-pack-v2.1.9.zip`（2.9 MB，含完整 `.git` 版本库，可 `git pull` 跟进上游）。

---

## 校验

`00_checksums/校验记录.txt` 记录了整合时的实测数据。发布包内 9 个文件全部对照官方 `SHA256SUMS.txt` 校验通过：

```
BOOTX64.EFI  335,872 字节  SHA256 9d2c41f9…
```

自行复核：

```bash
cd 01_release-v2.1.9
sha256sum -c SHA256SUMS.txt
```

---

## 许可与致谢

- 上游项目：[physicsdolphin/nv-uefi-unlocker](https://github.com/physicsdolphin/nv-uefi-unlocker)，作者 physicsdolphin，**GPL v2**
- 本仓库再分发的 `01_release-v2.1.9/`、`04_source/`、`03_docs-zh/` 均来自上游，版权归原作者，遵循 [GPL v2](04_source/LICENSE)
- 新增的中文文档（`操作教程.html`、`使用手册.html`、`快速上手.md`、`README.txt`）同样以 GPL v2 发布
- NVIDIA 固件与 VBIOS **不在本仓库内**，版权归 NVIDIA，需在目标机联网再生

## 免责声明

操作涉及关闭 Secure Boot 与向 GPU 寄存器写入。虽有易失性与断电复原的兜底，仍请自行评估风险，尤其在生产机上。本整合包不提供任何形式的担保。
