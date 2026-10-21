# Qt 6.8.0 → Qt 6.12.0 补丁审计

审计日期：2026-10-20。假定继续维护当前 deepin/Debian 包的功能和打包策略。

当前仓库：`04dbee260f5c7f73bf6cef87d3475456e2d05fee`，`6.8.0+dfsg-0deepin39`。
对照上游：[`v6.12.0`](https://github.com/qt/qtbase/tree/025bdad181de241e81bf853c8a2d7bf3d19261a9)，提交 `025bdad181de241e81bf853c8a2d7bf3d19261a9`。已通过 Qt 官方仓库和 GitHub 镜像的远端标签核实提交。

`debian/patches/series` 实际启用 **45** 项：**18 项建议保留或适配，1 项日志补丁可选，4 项按架构/构建场景决定，22 项可移除**。另有 6 项已注释补丁，见末尾。

“保留”指保留当前包的功能或策略；“需适配”表示旧补丁不能直接用于新源码。“可移除”表示 6.12.0 已有修复或等效实现。上游代码链接全部固定到上述提交，不随分支移动。

## 建议保留或适配：18 项

| 补丁 | 应用检查 | 原因及 6.12.0 源码证据 |
| --- | --- | --- |
| `add-ivs-variation-selector-support.patch` | 需适配 | HarfBuzz variation-glyph 回调仍忽略 variation_selector，QFontEngine::glyphIndex 仍只有一个参数；IVS 功能未被上游覆盖。旧补丁多处上下文冲突，应同步所有后端的虚函数声明和实现。 [src/gui/text/qharfbuzzng.cpp:494](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/text/qharfbuzzng.cpp#L494) |
| `fix-ivs-harfbuzz-run-boundaries.patch` | 正向通过 | 保留补充平面 IVS 与前一个字符使用同一字体 shaping run 的处理；与上一项配套。 [src/gui/text/qtextengine.cpp:1469](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/text/qtextengine.cpp#L1469) |
| `remove_privacy_breaches.diff` | 正向通过 | 继续删除文档模板中的外部社交 SDK/Google 脚本，保持 Debian 文档打包策略。 [doc/global/template/scripts/main.js:97](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/doc/global/template/scripts/main.js#L97) |
| `build_path_embedded_qtbuildinternalsextra_cmake.patch` | 正向通过 | 上游仍将 QT_SOURCE_TREE 构建路径写入安装的 CMake 文件；继续保留可重复构建处理。 [cmake/QtBuildInternalsExtra.cmake.in:84](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/cmake/QtBuildInternalsExtra.cmake.in#L84) |
| `cross.patch` | 正向通过 | 上游仍有清除 PKG_CONFIG_EXECUTABLE、限制交叉构建工具/示例的旧逻辑；debian/rules 仍使用 QT_FORCE_BUILD_TOOLS。 [cmake/QtBuildRepoHelpers.cmake:1139](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/cmake/QtBuildRepoHelpers.cmake#L1139) |
| `remove_rpath_from_examples.patch` | 正向通过 | 独立示例构建仍显式设置 CMAKE_INSTALL_RPATH；继续保留系统库打包策略。 [cmake/QtBuildRepoExamplesHelpers.cmake:147](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/cmake/QtBuildRepoExamplesHelpers.cmake#L147) |
| `enable_skip_plugins.patch` | 需适配 | 普通插件和 QML 插件的自动包含默认值仍为 OFF；保持发行版默认 ON。QML 部分已迁至 cmake/QtPublicPluginHelpers.cmake。 [cmake/QtPublicPluginHelpers.cmake:610](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/cmake/QtPublicPluginHelpers.cmake#L610) |
| `QT_FORCE_OPENGL_TYPE.patch` | 需适配 | 6.12.0 未提供此环境变量的处理；保持 deepin 的 OpenGL/OpenGLES 强制选择能力。createPlatformIntegration 的上下文已变化。 [src/gui/kernel/qguiapplication.cpp:1516](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/kernel/qguiapplication.cpp#L1516) |
| `add-single-finger-to-scroll-under-the-touch-screen.patch` | 顺序应用通过 | 上游没有 touchSlideTimer 方案；保持触屏单指 QListView 滚动功能。 [src/widgets/itemviews/qlistview.cpp:751](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/widgets/itemviews/qlistview.cpp#L751) |
| `fix-single-finger-to-scroll-under-the-touch-screen.patch` | 顺序应用通过 | 依赖前一补丁，保持拖动阈值、选择框清理和滚动结束清理。 [src/widgets/itemviews/qlistview.cpp:751](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/widgets/itemviews/qlistview.cpp#L751) |
| `fix-QListView-multiple-selection-conflicts-with-long-press-function.patch` | 顺序应用通过 | 保持触屏长按与多选的兼容处理。 [src/widgets/itemviews/qabstractitemview.cpp:4344](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/widgets/itemviews/qabstractitemview.cpp#L4344) |
| `fix-touch-screen-QListView-sliding-acceleration.patch` | 顺序应用通过 | 依赖单指滚动补丁，删除 MaximumVelocity=0 等设置；建议四项整理成一个最终补丁。 [src/widgets/itemviews/qlistview.cpp:751](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/widgets/itemviews/qlistview.cpp#L751) |
| `Fix-Optimized-Happy-Eyeballs-processing-in-HTTPS-mode.patch` | 需适配 | 上游仍在 TCP connected 时选择地址族；没有本地的 TLS encrypted 后决胜、h2Index 和 H2 队列迁移逻辑。不能因头部有 Upstream 链接而删除。 [src/network/access/qhttpnetworkconnectionchannel.cpp:947](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/network/access/qhttpnetworkconnectionchannel.cpp#L947) |
| `QTBUG-85978-85985-QlineEdit-Fix-the-problem-under-the-touch-screen.patch` | 正向通过 | 上游没有本补丁的 touchPress/touchPopup/doublePress 状态和延迟光标处理；继续保持现有触屏体验。 [src/widgets/widgets/qlineedit.cpp:1479](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/widgets/widgets/qlineedit.cpp#L1479) |
| `fix-touchpat-crush-from-xcb-nullptr.patch` | 正向通过 | pinch/swipe 两条路径仍直接对设备指针 Q_ASSERT；本地的运行时空指针保护仍有差异。 [src/plugins/platforms/xcb/qxcbconnection_xi2.cpp:1129](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/plugins/platforms/xcb/qxcbconnection_xi2.cpp#L1129) |
| `fix-xcb-Handle-DEVICE_ENABLED-in-XI2-hierarchy-events.patch` | 正向通过 | 上游 hierarchy 事件掩码仍未包含 DEVICE_ENABLED；保留触摸板禁用再启用后的设备刷新。 [src/plugins/platforms/xcb/qxcbconnection_xi2.cpp:1114](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/plugins/platforms/xcb/qxcbconnection_xi2.cpp#L1114) |
| `qt6-base-add-multi-seat-event.patch` | 正向通过 | 上游头文件已有带 QInputDevice 的重载声明，但 cpp 中缺少本地实现，processKeyEvent 也未把 e->device 传给 QKeyEvent。 [src/gui/kernel/qguiapplication.cpp:2623](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/kernel/qguiapplication.cpp#L2623) |
| `Fix-do-not-dispatch-events-while-closing.patch` | 需适配 | notifyInternal2 中仍没有本地新增的关闭检查；析构仍先清空实例再设关闭标志。6.12 已有 atomic_ref/g_self，应按新实现重写顺序，不能只移动旧版 self=nullptr。 [src/corelib/kernel/qcoreapplication.cpp:935](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/corelib/kernel/qcoreapplication.cpp#L935) |

## 日志补丁：1 项可选

| 补丁 | 处理 | 原因及证据 |
| --- | --- | --- |
| `be_verbose_on_plugin_inclusion.patch` | 可选，需适配 | 只增加插件包含状态日志，不是功能或安全修复。若保持现有打包日志则保留；QML 日志插入点已迁至 QtPublicPluginHelpers.cmake，可与 enable_skip_plugins 合并维护。 [cmake/QtPublicPluginHelpers.cmake:596](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/cmake/QtPublicPluginHelpers.cmake#L596) |

## 按目标架构和构建方式决定：4 项

| 补丁 | 应用检查 | 条件及证据 |
| --- | --- | --- |
| `Add-SH-detection.patch` | 正向通过 | 仅发布 SuperH/sh4 二进制时需要；6.12.0 的 SH 检测仍被注释。旧补丁写死小端，应与实际 SH 目标一致。 [src/corelib/global/qprocessordetection.h:320](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/corelib/global/qprocessordetection.h#L320) |
| `forkfd_grow_stack_upwards_on_hppa.patch` | 正向通过 | 仅支持 HPPA 时需要；上游仍将栈顶设为 childStack+sizeof(childStack)。 [src/3rdparty/forkfd/forkfd_linux.c:172](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/3rdparty/forkfd/forkfd_linux.c#L172) |
| `0001-deepin-sw_64-support.diff` | 需适配 | 发布申威 sw64 时保留；6.12.0 没有这套架构支持。forkfd 的 clone 架构列表增加了 sparc/m68k，刷新时保留这些上游项并追加 sw64。 [src/3rdparty/forkfd/forkfd_linux.c:88](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/3rdparty/forkfd/forkfd_linux.c#L88) |
| `CMake-Fix-Threads-Threads-global-target-promotion-is.patch` | 构建条件决定，需适配 | 6.12.0 在 configure.cmake 更早显式查找并提升 Threads::Threads，常规独立构建可能不再需要旧 workaround。若顶层提前创建非 GLOBAL 的 Threads target，仍需验证跨目录提升问题；若保留，应在首次 qt_find_package(Threads) 之前处理，而不是沿用旧位置。当前未实测该构建场景。 [configure.cmake:27](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/configure.cmake#L27) |

若继续发布 sw64，应保留申威补丁；若不发布 sh4/HPPA，则无需为这两个架构保留补丁。Threads 项需用实际构建入口验证，不能仅凭独立构建的源码结构判定所有场景都已解决。

## 可移除：22 项

| 补丁 | 判定依据及 6.12.0 源码证据 |
| --- | --- |
| `fix-QApplciation-with-a-QML-popupWindow.patch` | handleMouseEvent 等路径已改为检查 activePopupWidget，避免 QML popup 没有 QWidget 时解引用。 [src/widgets/kernel/qwidgetwindow.cpp:523](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/widgets/kernel/qwidgetwindow.cpp#L523) |
| `CVE-2025-5455_qDecodeDataUrl_FixMalformedCharsetHandling.patch` | data URL 解析已重构为分段处理，不再使用旧版无边界 data.at(i) 循环。 [src/corelib/io/qdataurl.cpp:47](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/corelib/io/qdataurl.cpp#L47) |
| `Use-modern-key-management-APIs.patch` | Schannel 已使用 NCrypt/CNG、现代 PKCS12 导入标志及私钥关联逻辑，并有后续改进。 [src/plugins/tls/schannel/qtls_schannel.cpp:2290](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/plugins/tls/schannel/qtls_schannel.cpp#L2290) |
| `Fix-heap-buffer-overflow.patch` | 已有 splitFrontMatter 的边界处理，CRLF 标记也已正确处理。 [src/gui/text/qtextmarkdownimporter.cpp:132](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/text/qtextmarkdownimporter.cpp#L132) |
| `Add-clamping-to-QColor-Transfer-Generic-Function.patch` | HLG/PQ 的输入 clamp 和 PQ 参考变换均已存在。 [src/gui/painting/qcolortransfergeneric_p.h:90](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/painting/qcolortransfergeneric_p.h#L90) |
| `Check-exactly-the-first-bit-of-XdndStatus-flags-in-QXcbDrag.patch` | 补丁可完整反向检查，最低位判断已存在。 [src/plugins/platforms/xcb/qxcbdrag.cpp:859](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/plugins/platforms/xcb/qxcbdrag.cpp#L859) |
| `fix-xcb-clear-stale-internal-state-when-setting-up-slave-devices.patch` | 补丁可完整反向检查，slave 设备状态清理已存在。 [src/plugins/platforms/xcb/qxcbconnection_xi2.cpp:242](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/plugins/platforms/xcb/qxcbconnection_xi2.cpp#L242) |
| `fix-QLabel-Reset-link-hover-state.patch` | QLabel 已转发 Enter/Leave，文本控件已有更新/清空 highlightedAnchor 的辅助方法。 [src/widgets/widgets/qwidgettextcontrol.cpp:2955](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/widgets/widgets/qwidgettextcontrol.cpp#L2955) |
| `QDom-Change-default-InvalidDataPolicy-to-ReturnNullNode.patch` | 补丁可完整反向检查，默认策略已是 ReturnNullNode。 [src/xml/dom/qdom.cpp:95](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/xml/dom/qdom.cpp#L95) |
| `CVE-2026-16762_fix-quadratic-behavior-in-resolvetag.patch` | resolveTag 已使用 QDuplicateTracker<AttributeName>，消除属性重复检查的二次复杂度；实现有后续简化。 [src/corelib/serialization/qxmlstream.cpp:1911](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/corelib/serialization/qxmlstream.cpp#L1911) |
| `Add-truncated-entries-to-QLocale-uiLanguages.patch` | 自 6.9 起 uiLanguages 已包含合理的截断回退项，QTranslator 也不再在每项内逐级截断。 [src/corelib/text/qlocale.cpp:5413](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/corelib/text/qlocale.cpp#L5413) |
| `CVE-2026-11573_fix-stack-overflow-in-qdom-serialization.patch` | 已有 saveSubTree 的非递归遍历和 afterSave。 [src/xml/dom/qdom.cpp:1509](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/xml/dom/qdom.cpp#L1509) |
| `CVE-2026-76151_port-nextNonWhitespace-to-qsizetype.patch` | nextNonWhitespace 及 cookie/Cache-Control 解析的位置和长度均已迁至 qsizetype。 [src/network/access/qnetworkcookie_p.h:49](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/network/access/qnetworkcookie_p.h#L49) |
| `CVE-2026-19248_fix-unbounded-nesting-depth-in-QDomNode-dtor.patch` | 析构已使用迭代后序遍历；上游还包含深层析构/clear 回归用例。 [src/xml/dom/qdom.cpp:1088](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/xml/dom/qdom.cpp#L1088) |
| `CVE-2026-78253_fix-stack-exhaustion-in-readElementText.patch` | 补丁可完整反向检查，readElementText 已使用 nestingLevel 计数。 [src/corelib/serialization/qxmlstream.cpp:2462](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/corelib/serialization/qxmlstream.cpp#L2462) |
| `CVE-2026-82292_guard-against-null-nodes-in-QDomBuilder.patch` | characters/skippedEntity/comment 均已检查工厂返回空节点；上游包含相关回归用例。 [src/xml/dom/qdomhelpers.cpp:135](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/xml/dom/qdomhelpers.cpp#L135) |
| `fix-ComboBox-Wheelevent.patch` | 不允许滚轮切换时已调用 e->ignore()，并有 ignoreWheelEvents 用例。 [src/widgets/widgets/qcombobox.cpp:3619](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/widgets/widgets/qcombobox.cpp#L3619) |
| `Fix-crash-when-fontconfig-fallback-pattern-has-no-charset.patch` | 补丁可完整反向检查，Fontconfig charset 缺失保护已存在。 [src/gui/text/unix/qfontenginemultifontconfig.cpp:31](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/text/unix/qfontenginemultifontconfig.cpp#L31) |
| `CVE-2026-95620_qhttp2stream-fix-upload-device-use-after-free.patch` | uploadDeviceDestroyed 已清空两个设备指针和 owning 标志，包含对应回归用例。 [src/network/access/qhttp2connection.cpp:652](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/network/access/qhttp2connection.cpp#L652) |
| `CVE-2026-96432_qhttp2connection-guard-null-qpointer.patch` | RST_STREAM 使用 value()，PUSH_PROMISE 检查空 QPointer，包含对应回归用例。 [src/network/access/qhttp2connection.cpp:1912](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/network/access/qhttp2connection.cpp#L1912) |
| `CVE-2026-105923_clamp-coordinates-in-qtriangulator.patch` | 补丁可完整反向检查，坐标 clamp 和回归测试已存在。 [src/gui/painting/qtriangulator.cpp:29](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/painting/qtriangulator.cpp#L29) |
| `CVE-2026-107157_qcborvalue-fix-buffer-overread-fromcbor.patch` | 补丁可完整反向检查，fromCbor 不再把 qsizetype 长度截断为 int。 [src/corelib/serialization/qcborvalue.h:246](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/corelib/serialization/qcborvalue.h#L246) |

以上包含当前启用的全部 11 个 `CVE-*` 补丁；可从 6.12.0 补丁队列删除这些回移植文件，不表示移除上游安全修复。本次只核对现有补丁对应的代码修复，没有独立核验文件名所写 CVE 编号。

## 已注释的 6 项

| 补丁 | 升级时处理 |
| --- | --- |
| `armel-noyield.patch` | 不重新启用。`qYieldCpu` 已移至 `src/corelib/thread/qyieldcpu.h`，GNU ARM 汇编 yield 路径已有 ARM >= 7 条件。[src/corelib/thread/qyieldcpu.h:52](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/corelib/thread/qyieldcpu.h#L52) |
| `deepin-Allow-QPalettePrivate-to-be-used-outside-of-qpalette-cpp.patch` | 不重新启用。所需 constexpr 方法已在私有头文件内定义。[src/gui/kernel/qpalette_p.h:54](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/kernel/qpalette_p.h#L54) |
| `Make-sure-hicolor-searched-before-dashfallbacks.patch` | 不重新启用。已有 DashRule、父主题优先查找和后续 dash fallback。[src/gui/image/qiconloader.cpp:564](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/image/qiconloader.cpp#L564) |
| `add_QT_IM_MODULES_env_to_allows_multi_IM_key.patch` | 不重新启用。QT_IM_MODULES 和列表回退已存在。[src/gui/kernel/qplatforminputcontextfactory.cpp:35](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/src/gui/kernel/qplatforminputcontextfactory.cpp#L35) |
| `upstream_Ensure-that-libzstd-targets-are-promoted-to-global-i.patch` | 不重新启用。WrapZSTD 的 PROVIDED_TARGETS 已列出三种 zstd target。[configure.cmake:124](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/configure.cmake#L124) |
| `upstream_Prefer-using-the-non-suffixed-libzstd-over-static-on.patch` | 不重新启用。已优先 shared、再非后缀 libzstd、最后 static；补丁可反向检查。[cmake/FindWrapZSTD.cmake:33](https://github.com/qt/qtbase/blob/025bdad181de241e81bf853c8a2d7bf3d19261a9/cmake/FindWrapZSTD.cmake#L33) |

## 迁移时优先处理的冲突

1. **IVS**：更新字体引擎虚函数及所有平台后端；保留 run-boundaries 修复。
2. **插件默认策略和日志**：QML 部分从 `QtPostProcessHelpers.cmake` 移至 `QtPublicPluginHelpers.cmake`。
3. **Happy Eyeballs**：重新适配 `_q_startNextRequest` 和私有成员上下文；验证 IPv4/IPv6、TLS 握手失败回退及 HTTP/2 队列归属。旧补丁的 Upstream 字段不能证明整个本地补丁已合入。
4. **关闭事件派发**：按 6.12 的 `self`/`g_self`/`atomic_ref` 实现重写。已有 `QCoreApplication::notify` 的关闭保护，仍不等同于本地 `notifyInternal2` 的改动。
5. **sw64**：更新 forkfd 架构列表上下文。
6. **Threads**：如保留保护，移到 6.12 首次 `qt_find_package(Threads)` 之前；当前旧补丁位置太晚。

## 验证范围

- 已读取本地所有补丁和 series，对照固定的 6.12.0 源码逐项检查差异。
- 对 45 个启用补丁执行独立 `git apply --check` 和 `git apply -R --check`：13 项正向通过，7 项完整反向通过。其余通过源码核对区分已合入、上下文变化和本地功能；没有把应用失败直接当作已合入。
- 在 `/tmp/qt612-touch-chain-check` 中按当前 series 顺序实际应用 QListView 的四个触屏补丁，全部通过。第二和第四个补丁依赖前序补丁，不能单独对干净源码判断是否适用。
- 以上是源码和补丁应用审计，未编译 Qt、未运行 Qt 回归测试，也未完成触屏、IVS、多座席或双栈 HTTPS 的运行验证。
- 未修改现有源码、补丁或 series；本报告是新增文件。

复核命令（`QT612_SOURCE` 指向干净的固定提交源码；`PATCH_FILE` 为补丁绝对路径）：

```sh
git -C "$QT612_SOURCE" rev-parse HEAD
git -C "$QT612_SOURCE" apply --check "$PATCH_FILE"
git -C "$QT612_SOURCE" apply -R --check "$PATCH_FILE"
```

## 与 Debian 官方补丁队列的关系

2026-10-20 另行核对了 Debian 官方 Salsa 仓库的 master：`e5d8d224144f75c8fe58784090eda684b462cc8e`，打包版本 `6.11.2+dfsg-6`。同时核对了 deepin 分叉基线对应的 Debian `6.6.1+dfsg-5` 标签（提交 `8347eb6776712bfae4eb4fd60bc69ba299c0e522`）。

来源：[Debian 当前 series](https://salsa.debian.org/qt-kde-team/qt6/qt6-base/-/blob/e5d8d224144f75c8fe58784090eda684b462cc8e/debian/patches/series)、[Debian 6.6.1 series](https://salsa.debian.org/qt-kde-team/qt6/qt6-base/-/blob/8347eb6776712bfae4eb4fd60bc69ba299c0e522/debian/patches/series)。

“deepin 额外携带”在此表示本目录携带、而该 Debian 版本的补丁队列没有的改动。它不等于功能只能在 deepin 使用，也不证明其他发行版从未采用。

在前面建议保留或按条件保留的补丁中，以下 **15 项属于需要额外审查的本地补丁**，Debian 当前队列没有对应项：

| 类型 | 补丁 | 性质 |
| --- | --- | --- |
| IVS 异体字 | `add-ivs-variation-selector-support.patch`、`fix-ivs-harfbuzz-run-boundaries.patch` | deepin 增加的字体 shaping 功能 |
| QListView 触屏 | `add-single-finger-to-scroll-under-the-touch-screen.patch`、`fix-single-finger-to-scroll-under-the-touch-screen.patch`、`fix-QListView-multiple-selection-conflicts-with-long-press-function.patch`、`fix-touch-screen-QListView-sliding-acceleration.patch` | deepin 增加的单指滚动、长按/多选和惯性滚动调整 |
| QLineEdit 触屏 | `QTBUG-85978-85985-QlineEdit-Fix-the-problem-under-the-touch-screen.patch` | deepin 对触屏光标、长按和双击行为的调整 |
| OpenGL 类型选择 | `QT_FORCE_OPENGL_TYPE.patch` | deepin 增加的环境变量 |
| 申威 | `0001-deepin-sw_64-support.diff` | deepin 增加的 sw64 支持 |
| 多座席输入 | `qt6-base-add-multi-seat-event.patch` | 补充带输入设备的键盘事件传递 |
| XCB 手势设备空指针 | `fix-touchpat-crush-from-xcb-nullptr.patch` | 通用缺陷的本地修复 |
| XCB 设备重新启用 | `fix-xcb-Handle-DEVICE_ENABLED-in-XI2-hierarchy-events.patch` | 通用缺陷的本地修复 |
| 双栈 HTTPS/HTTP2 | `Fix-Optimized-Happy-Eyeballs-processing-in-HTTPS-mode.patch` | 含本地差异的网络修复，不能把整个文件当成已合入的上游补丁 |
| 关闭时事件派发 | `Fix-do-not-dispatch-events-while-closing.patch` | 通用缺陷修复，6.12.0 尚未覆盖本地全部改动 |
| CMake Threads target | `CMake-Fix-Threads-Threads-global-target-promotion-is.patch` | 构建场景相关的本地 workaround；是否保留仍需实际构建验证 |

其中前六类共 **10 项**属于功能或平台支持扩展；后五项属于本地缺陷修复/workaround。它们在当前仓库的添加记录中可追溯至 deepin 提交。是否继续携带，应同时依据上面的 6.12.0 覆盖情况和需要维持的行为。

以下 **8 项继承自 Debian**，不能算 deepin 特有：`Add-SH-detection.patch`、`be_verbose_on_plugin_inclusion.patch`、`remove_privacy_breaches.diff`、`build_path_embedded_qtbuildinternalsextra_cmake.patch`、`cross.patch`、`remove_rpath_from_examples.patch`、`forkfd_grow_stack_upwards_on_hppa.patch`、`enable_skip_plugins.patch`。当前 Debian 仍有其中 7 个同名文件；日志补丁可在旧版 Debian 标签中找到。`cross.patch` 和插件补丁随 Qt 版本有过内容调整，来源相同不表示文件逐字相同。

前面可移除的 22 项主要是上游修复的回移植，不能因它们在 Debian 队列中没有同名文件，就把它们当成 deepin 独有功能。例如本地 `QDom-Change-default-InvalidDataPolicy-to-ReturnNullNode.patch` 与 Debian 的 `upstream_87c5b3e_CVE-2026-15037.patch` 名称不同，但实际增加/删除的代码行一致。
