# tmlr：合并与修订说明（2026-11-19）

以 `tmlr_revision/`（较新，含缺失事件 + 反馈控制实验，事实性修正较多）为底稿，`tmlr_version/` 中仍在主 claim 范围内的内容已并入。两个原目录已于同日打包为 `../tmlr_drafts_20261119_src.tar.gz` 并删除，现在只保留本目录这一个 TMLR 版本。

编译命令：`~/bin/tectonic -r 3 main.tex`。共 20 页，正文 7.5 页（revision 为 9 页），没有未定义的引用或 citation。

## 主 claim（现在写在摘要和引言的 "Main claim" 段）

> 已知循环结构只在受主导不确定性限制的那个组件上起作用；某一组件上的提升不一定能传递到下一组件。

- 增量可观测时，结构应放在**转移**上：输入条件化相位旋转能精确计数，并能从终端奖励学会小 k（§3–4）。
- 增量缺失或含噪时，结构应放在**类别型信念更新**上，相位旋转的优势随之消失（§4.3、§5）。
- 后验质量提升后，固定 planner 下的控制效用差异小于 1 个百分点（§5）。

由此得出结论：表示、估计、控制三者必须分开评估。

## 结构调整

| 原位置 | 处理 | 原因 |
|---|---|---|
| §4 Theoretical Analysis，Prop 1 | 并入 §3，标注 "cf. Grazzi; Karuvally" | 该构造在已有工作中出现过，单独成节会夸大理论贡献 |
| Prop 2（全局相位不变性）及讨论 | 移到附录 A | 只涉及 Born 读出，与主 claim 无关 |
| Prop 3（收缩与鲁棒解码） | 陈述移到附录 C | 只是辅助论证 |
| §5 Generalization with Terminal State Labels | 移到附录（lengthgen_arch） | 监督设定，偏离主线；`born_char` 原实现缺失 |
| §6.3 读出消融 | 改写为 "The transition, not the readout, carries the structure" | 只保留支撑 claim 的那一点 |
| 附录 Robot-Control Transfer（RLBench reach、tab_downstream、tab_robot）及 "boundary transfers downstream" 段 | 删除；valve 来源说明保留为附录 "Valve Task Scope and Provenance" | 偏离主 claim，冻结 tracker 的迁移不属于这三个问题中的任何一个 |
| 附录 Gleason 定理与"量子幅度"讨论 | 删除 | 量子叙事与主 claim 无关 |
| tmlr_version 的 Related Work | 只并入 "length generalization on group word problems" 一段，并压缩 | 支撑 Q1；"intersection of three lines" 一类的 novelty 包装已删除 |
| 引言 Contributions | 重写为 3 条，与三个问题一一对应 | |

## 写作

- 去除 which / what / when / where / whose 引导的定语从句（全文 grep 已清零），改为分词短语、独立句、并列句或名词化表达。例："in which the count is carried by …" → "the unit-modulus coordinate … carries the count"；"which equals the chance value 1/k exactly when …" → "This bound equals 1/k exactly for a uniform residue"。
- 修复 feedback 节公式的 bug：align 中 `,\` 应为 `,\\`，原先两个方程挤在同一行。

## 图

- **图 1**：PPTX 是唯一源文件（`figures_src/fig_overview.py` 生成 `figures/fig_overview.pptx`），全部用原生形状和文本框画成，字体统一为 Arial。
  - 版式：三张卡片（Represent / Estimate / Control），每张卡片带编号徽标和彩色顶条，底部是结果框。
  - 结果框内的数字都取自正文表格：LRU parity 0.64→1.00、valve 0.998、噪声 k=4 时 0.99 vs 0.25、NLL 0.94 vs 1.01、效用 0.630 vs 0.623。
  - 信念柱下方标注了信念熵 H（单位 nats）。
  - PDF 由 `figures_src/pptx_to_pdf.py` 直接读取 PPTX 渲染，使用与 Arial 度量相同的 Liberation Sans（字体文件在 `figures_src/fonts/`），因此不会和 PPT 内容脱节。
  - 在 PowerPoint 中修改后，可以"另存为 PDF"覆盖 `figures/fig_overview.pdf`，也可以重新运行 `pptx_to_pdf.py` 生成。
- **数据图字体**：同样改为 Liberation Sans，数学符号也统一，与图 1 一致。
- **数据图**：由 `figures_src/figures_data.py` 重新生成，数据与原脚本相同。
  - noisy_horizon 高度从 2.30 in 降到 1.78 in：去掉子图标题，k 标注放进图内，y 刻度精简，图例单行。
  - feedback_results 高度从 2.53 in 降到 1.95 in：面板标题移到图内。
  - readout_comparison 高度从 3.65 in 降到 2.75 in：下排补上了原先缺失的方法名刻度。
- **fig_rl、fig_phase**：找不到生成脚本，只做了 PDF 边界裁剪。fig_rl 图例中仍有 "Born (ours)"，Transformer 参数数为 5.9k，与 Table S2 的 20k 不一致，需要从原始数据重画。

## 第二轮修订（同日）

- **标题**：改为 *Structure Follows Uncertainty: Cyclic State Estimation and Control under Partial Observation*。
- **主 claim 的 significance**：改写为"在部分可观测下，结构先验的价值取决于不确定性的进入位置，而不是架构的表达能力"。
  - 引言加了一段，说明 state-tracking 表达能力研究与 POMDP 实践之间的张力。
  - 新增两个推论：
    - (i) 在干净增量上测得的表达能力排名，换到含噪增量后会反转：parity 上 LSTM 从 chance 升到 0.89–0.93，相位旋转则降到 chance。
    - (ii) NLL 会高估估计器对控制的价值，应改用固定 planner 来评估。
  - 贡献 (3) 改写为一个 fixed-planner 评估协议。
- **正文表格不再有空格**：
  - Table 1 中 GRU/LSTM 的中位穿越步数填为 ">7.68M"。依据是 `quantum-IL/paper/data/rl_curves.json`：两者预算都是 7.68M，5 个 seed 都没有穿越阈值。
  - Valve 表只保留"按验证集选学习率"这一组：所有模型都有数，并补上 K=8 列；固定学习率那组放在附录。
  - oracle 从表中移到 caption 里说明；附录中与正文重复的那张表已删除。
- **Limitations 前移**：限制条件改写为 §3.3 "Scope of the claim"，放在实验之前说明。
- **Discussion 与 Conclusion 分开**：
  - Discussion（§6）有三段：表达能力只在增量可观测时才是瓶颈（机制：学到的角度误差随长度线性累积，类别平移在构造上精确）；后验质量与决策价值（校准主要影响 sensing 决策）；一组结构先验的诊断顺序。
  - Conclusion（§7）只有一段：先给出原则，再说明设计与评估上的含义，最后落到下一步的可检验预测（真实传感器、周期需要学习的情形）。
- **章节标题润色**：各实验节标题直接写出结论，例如 "Observed Increments: Structure Belongs in the Transition"、"Calibration gains barely change control"。

## 第三轮：Fig 2 / Table 2 排版

- Fig 2、Table 2 原先用 `[tbp]`，被排到一个只放浮动体的页面，上下留了大片空白。现在改为 `[!t]`，两者在同一页顶部依次排列，§5 正文紧接在下面。
- Table 2 按记忆类型分组（相位旋转 / 类别型 / state-tracking 层 / 门控循环），用 multirow 标注，每列最优值加粗，数值统一写成 "mean ± SD"，caption 也压缩了。
- §5 的 Table 3 和 Fig 3 同样改为 `[!t]`，并把它们在源文件中的位置提前到 §5.1，避免第 7 页底部留白。全文仍为 20 页。

## 第四轮：Table 3 重排
- 列分组为 Estimation / Control / Gap to matched Bayes；新增配对差值 ΔNLL 与 ΔUtility（百分点，mean±SD，来自 runs/followup/block2/screen-pxs7skb3/summary.json）。
- 行按先验分组：None（GRU）/ Phase rotation（Born rotor）/ Cyclic filter / Exact Bayes；删除 Success 列（已含于 Utility）。
- caption 首句直接陈述结论：NLL 差最高 0.09 nats，效用差最多约 1 个百分点。
- GRU NLL 按原始均值改为 1.011；Discussion 中 0.075 改为配对值 0.074。

## 第五轮：C1 规划区间扫描（新实验）
- 代码与数据：runs/followup/block2/c1_sweep/{sweep.py,analyze.py,results/,summary.json}。复用 20 个冻结测试检查点与原评估历史，不重训；只改 planner：H∈{1,2,3,5,7} × c∈{0,.02,.08,.2,.4} × ρ∈{.6,.8,.95}（75 点，53 个不同区间）。原区间用原 draws 逐 seed 精确复现 Table 3 效用。
- 结论：headroom（matched−uniform）0.3–26 pts；任何学习估计器对 matched Bayes 的损失 ≤1.47 pts，token-constrained filter ≤0.66；headroom≥10 时保留 ≥90%（filter ≥95%）。小 headroom（感知便宜可靠）时相对损失变大，GRU 因过度自信少 sense，3 个区间低于 uniform（≤0.92 pts）。filter vs GRU 从不显著更差，47/75 显著更好。
- 论文：新增 §5.3 + Fig 4（figures_src/fig_regimes.py）、附录 K + Table（tab:regimes）；摘要、引言、贡献 (3)、Discussion、Conclusion、Fig 1 call-out 同步为“75 个区间内 ≤1.5 pts，校准主要通过 sensing 决策起作用”。

## Round 6 (2026-11-21): recorded inertial sensor replaces the spectrometer replay in the main text
- New Section 6 "Heading from a Recorded Inertial Sensor" (sections/imu.tex, fig:imu = figures/imu_heading.pdf,
  figures_src/fig_imu.py) and Appendix "Recorded Inertial Sensor Protocol and Results" (sections/imu_details.tex,
  tab:imu-a, tab:imu-contrast, tab:imu-b). Data: BROAD (Laidig et al. 2021); experiment code and pre-registered
  protocol with all amendments in runs/followup/real_imu/ (PROTOCOL.md, imu_tables.py prints every number).
- Task A (gyro increments): input-set rotation beats GRU/LSTM/Transformer on error and NLL at all lengths 10-240 s
  (240 s: 29.6 vs 73.7 deg, -44.1 [-65.5, -31.4]); matches integrating u_t; VQF 6D (bias estimation) is better.
- Task B (magnetometer pointer): cyclic filter ties the networks; pre-registered criterion not met; reported as such.
- Spectrometer replay (former Sec 5.5 + Fig) moved to Appendix app:speckle-replay; it tests measurement selection,
  not the architecture.
- Abstract last sentence, intro roadmap, and conclusion updated; three bib entries added.
- Round 6b: new Figure fig:imu-sensor (myon aktos-t IMU schematic, raw gyro, belief traces on test trial 20 from 40 s; seed-0 retrain reproduces saved test errors exactly). Abstract rewritten to frame valve + sensor heading and report both IMU tasks.

## Round 7 (2026-11-21): 7 -> 5 main sections, readability pass
- 1 Introduction now carries a findings list (one bullet per observation regime + recorded sensor) and Related Work as §1.1
  (adds an inertial-heading paragraph). sections/related.tex removed.
- 2 "Two Cyclic Structures": phase rotation + new §2.3 cyclic filter (sec:filter, eq:filter-predict/correct, moved from old §5).
- 3 "Simulated Experiments" merges old §4 and §5: clean events / noisy events / missing events (sec:feedback) /
  acting on the belief (sec:regimes). sections/feedback.tex removed; all labels kept.
- 4 Recorded inertial sensor split into §4.1 gyroscope and §4.2 magnetometer.
- Every subsection of §3–§4 opens with a one-sentence bold "Finding".
- 5 Conclusion split into three paragraphs: what we found, what the comparisons control, where the cycle belongs
  (main claim last; scope is not the closing paragraph). Remaining wh-relative clauses removed.

## Round 8 (2026-11-21): readability = prose, not signposts
- Reverted the bold "Finding" lines (§3–§4), the intro findings list, and the bold paragraph labels in the
  conclusion. The intro results summary is a prose paragraph again.
- Prose pass over intro, method, §3, §4, conclusion: joined choppy sentences, added transitions, removed
  repetition; numbers unchanged.
- Moved to appendix: proof of Prop. 1 (app:proofs), Assumptions and Scope (sections/scope.tex), RL training
  hyperparameters (app:additional), token-constraint and entropy numbers (app:feedbackdetails).
- Fig 5 (imu_sensor) redrawn: optical truth in black on both belief panels, model beliefs in model colors,
  new error strip (13 vs 85 deg mean after 10 s on the shown window).
- Round 8b: Figs 5+6 merged into one IMU figure (imu_main): (a) same inputs, ours vs learned-update models; (b) one held-out window, unwrapped heading + error; (c) error and (d) NLL vs window length on all test trials.
- Round 9 (figure audit): Fig 2 (noisy_horizon) replaced by tables/tab_noisy.tex (L=14 vs 160, k=2,3,4; full data in
  app:additional). Fig 3 (feedback_results) moved to app:feedbackdetails (duplicated Table 3). Fig 4 (fig_regimes)
  reduced to the utility-gap panel. Fig 1 panel (c) now shows the two structures instead of the planner icons.
  Main text ends on p. 9 (was p. 10).
