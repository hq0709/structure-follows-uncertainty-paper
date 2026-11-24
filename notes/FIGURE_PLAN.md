# 图的修改计划（2026-11-23）

数据路径都是相对于本地代码目录的，即 `/raid/home/CAMCA/hj880/quantum-IL-followup/code/`。原有画图脚本在 `paper_figures_src/`，构建说明见 `BUILD.txt`。正文目前有 3 张图，3.1 和 3.2 两节一张图都没有，要读出主论点只能看表。

## 总体规范（所有图统一）

- 字体统一用无衬线体（Liberation Sans 或 Arial，原 `paper_figures_src/fonts/` 里已有），数学符号也一样。现在 Fig 3 是 DejaVu Serif，和其他图不一致。
- 颜色按"结构放在哪里"编码，全文固定不变：
  - 转移上的结构（phase rotation）：赭红 #A0442C
  - belief 更新上的结构（cyclic filter）：墨绿 #0E7C66
  - 无结构（GRU/LSTM/Transformer）：蓝灰 #5B7DB1，三者用不同 marker 区分
  - Bayes 和经典滤波器（参照）：黑色或灰色虚线
- 单栏宽 6.5 in（TMLR 单栏）。字号不小于 8 pt，图例尽量放进坐标区，不另占一列。
- 各子图用 (a)(b)(c) 编号，标号写在左上角，不加子图标题框。

## Fig 1（第 1 页）：重画成"论点 + 架构"总览图 ★最重要

现状：三个小示意图之间留白很大，(c) 两个图标表达不出两种结构的区别，也看不出论点。

建议版式，横向三栏，高约 2.3 in：

1. **左栏：不确定性从哪里进入。** 画一个 Z_4 的环（阀门 4 个位置），在旁边标出 3 个入口：① 增量被准确观测到（实线箭头 +1）；② 增量含噪或缺失（虚线箭头，标 "?"）；③ 指针读数被干扰（指针图标加闪电）。
2. **中栏：统一的估计流程。** 观测 o_t → encoder → transition → belief correction → readout → shared planner。三个入口各对应一个着色模块：
   - ① 对应 transition 模块（赭红）：z ← z·e^{iθ(u_t)}，θ 由测得的增量决定；
   - ② 对应 belief update 模块（墨绿）：b̃ = Σ_j q(j) P_j b，配一个小柱状图表示"平移再混合"；
   - ③ 对应 pointer likelihood / bias 模块：在 IMU 场景里就是 bias 状态和 rest gate。
3. **右栏：证据带。** 每个入口配一个结果小卡片，数字取自正文：
   - ① 终端奖励训练时，只有带转移结构的模型学得会（无结构的模型 NLL 1.386，等于随机水平）；IMU 上从 10 s 训练外推到 240 s，误差 29.6°，网络是 73.7°；
   - ② 误读率 1% 时，misread-aware filter 的 NLL 0.966，Bayes 0.965，而把 token 当作确定量的结构是 5.84；
   - ③ 加入 bias 状态后，120 s 的误差从 20.2° 降到 12.8°。

可以先用 `figure-spec` 生成可编辑的矢量草图，你在 PPT 或 AI 里再调整；也可以继续用原来的 PPTX 流程（`paper_figures_src/fig_overview.py`）。

## 新 Fig：不确定性扫描的交叉图（放在新的 Sec. sec:sweep）★核心证据

数据：`runs/followup/uncertainty_sweep/run-combined/summary.json`，字段包括 regime、rate、signal、model、length、nll、nll_sd、accuracy。

- 三个子图横排，对应 dense / terminal / reward 三种训练信号。横轴是误读率 r（0, .005, .01, .02, .05，用 symlog 刻度），纵轴是 L=40 时的 NLL 减去 Bayes 的 NLL（即"相对 Bayes 的额外 NLL"，0 为最优，用对数刻度或截断在 3）。
- 每个子图画 5 条线：GRU、LSTM、learned-angle rotation、measured-angle rotation、misread-aware filter。token-constrained filter 的值太大（5.8 到 13），用一个箭头标注即可，或者放到附录。
- 想表达的信息：r=0 时，带结构的模型在三种信号下都贴着 0；r>0 时只有 misread-aware filter 仍然贴着 0；无结构的模型在 terminal 和 reward 信号下一直停在随机水平。
- 可以再加第 4 个子图，画 missing regime（L=160，r 从 0 到 .3），表明同样的规律在有缺测和指针时依然成立。

## 新 Fig：长度外推曲线（放在 Sec. sec:rl 或 sec:transfer）

数据：
- parity 和 Z_3：`quantum-IL/paper/data/rl_curves.json`、`modk_study.json`。原来的 fig_rl 和 fig_phase 的生成脚本已经丢失，需要从这两个 JSON 重画。注意 fig_rl 图例里的 "Born (ours)" 要改成 "Phase rotation"，Transformer 的参数量要和表一致（20k）。
- valve 外推：`runs/followup/valve_retune/test/*.json`，每个文件 `history[-1]` 里有 success_K8、success_K32、success_K128；按 (policy, hidden) 分组，每组 5 个 seed。

建议画法：横轴为测试长度或转动次数 N（8, 32, 128，对数刻度），纵轴为成功率；带阴影的误差带表示 ±SD。phase rotation 用赭红，基线只画每个家族里最好的那个宽度，再用一条灰色虚线标出训练范围的上限（N=8）。

## Fig 2（规划器扫描，fig_regimes）：重画

现状：图例放在右侧外面，4 种估计器的点大量重叠，对数横轴不直观，看不出"都在 1.5 分以内"这个结论。

数据：`runs/followup/block2/c1_sweep/summary.json`，脚本是 `paper_figures_src/fig_regimes.py`。

建议：
- 横轴按 estimation headroom 分 3 箱（<3、3–10、≥10 pts），每箱画各估计器 "retained share"（保留的可得收益百分比）的箱线图或 strip 图；
- 或者保留散点，但只画 GRU 和 token-constrained filter 两类，再加一条 LOESS 平滑线；
- 图例放进坐标区内。要突出的结论是：cyclic filter 保留至少 95% 的可得收益，并且从不低于 GRU。

## Fig 3（IMU，imu_main）：重排版，并加入新模型

数据：`runs/followup/real_imu/taskA_summary_round4.json`，字段为每个模型在每个长度上的 err_deg 和 nll_last。新增两条线：rotor_rest（"+ bias state"）和 gru_resid（"GRU on integrated increment"）。逐窗口的数据在 `runs/followup/real_imu/runs4/rotor_rest_lr3e-3_s*.json`。

建议：
- (a) 示意图单独重画，字号不小于 8 pt；可以和 Fig 1 中栏的 IMU 版本合并，这样 (a) 就能删掉。
- (b) 单条 trace：GRU 那条线太乱，会盖住主体。可以只画误差曲线（上半部分保留 optical truth 和我们的模型），GRU 用浅色细线。
- (c)(d)：加入 "+ bias state" 一条线（实线，深赭红）和 "GRU on integrated increment" 一条线（虚线，蓝灰）。VQF 保持灰色点划线。

## Fig S1（feedback_results）

偏小，(a)(b) 的字号不一致。按上面的统一规范重出即可，数据和脚本都在 `paper_figures_src/figures_data.py`。

## 表格（顺便统一）

- 小数位统一：准确率和成功率一律保留 3 位（.500 而不是 .50），NLL 也保留 3 位。
- 均值和误差的写法统一为 "mean ± SD"。现在 Table 3 用的是 "(SD)"，其他表用 ±。
- 表 4（tab_feedback）有 9 列太密，可以把 Gap 两列移到附录，或者把 Params 列挪进 caption。
