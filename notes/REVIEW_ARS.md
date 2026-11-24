# 审稿报告：Cyclic State Estimation and Control under Partial Observation（tmlr）

方法：按 academic-research-skills / `academic-paper-reviewer` 的 full 模式结构执行，即 Journal-Fit Reviewer + 方法 / 领域 / 跨视角三位审稿人 + Devil's Advocate，再做编辑综合。该 skill 原本要求在 Phase 0 停下来确认审稿人配置，这一步跳过了，直接按 TMLR 的 ML/RL 审稿视角配置。状态为 `NOT_CALIBRATED`（skill 默认），属于作者自查用途，不能当作真实审稿结论。

## 编辑决定：Major Revision

合并后叙事一致：三个问题对应三个实验块，主 claim 有证据支撑。主要风险有两点：一是第三个论点（估计 → 控制增益很小）可能部分由实验设计决定；二是 §4 和 §5 的训练信号不同，与"不确定性来源"这一变量混杂。

## CRITICAL

**C1（DA）控制差异小，可能是协议本身造成的。** [已处理：第五轮规划区间扫描，见 §5.3 / 附录 K] planner 最多 3 步，sense 代价 0.08，ρ=0.8，终端熵约 0.83 nats。在这一区间，所有估计器给出的最优动作本来就高度重合。审稿人会问：存在一个校准确实重要的区间吗？如果不存在，"估计提升不传递到控制"只是这个 horizon 和代价下的性质。
- 建议：对 sensing cost ∈ {0.02, 0.08, 0.2}、最大步数 ∈ {3, 6}、ρ ∈ {0.6, 0.8} 做网格扫描，报告效用差随参数的变化。
- 这一组实验可以把论点 (3) 从"在测试设置下差异小"提升为"差异何时变大"。
- 按"不收缩主张"的原则：如果扫描后差异在任何区间都很小，就把它作为发现直接写；如果某些区间差异变大，就写出这个边界。

## MAJOR

**M1（方法）§4 与 §5 的训练信号不同。** §4 是终端奖励 RL，§5 是逐前缀监督 NLL。主 claim 把差异归因于不确定性来源，但训练信号也变了。至少要在正文明确声明这一点。更好的做法是在 §5 的设置下补一个用终端奖励训练的估计器（哪怕只跑 Born 和 token-constrained filter 两个）。

**M2（方法）sample efficiency 的比较对象不对等。** Transformer 的学习率、预算、位置编码都不同。正文已经如实写出，但贡献 (1) 仍把它作为主要证据之一。建议补一组 matched-lr、matched-budget 的 Transformer。做不到的话，贡献 (1) 应以"转移条件化"（Table S2 中 LRU 从 0.64 到 0.996）为主证据，样本效率只作为辅证。

**M3（领域）两个"categorical"模型的关系没有交代。** §4.3 的 "learned categorical cyclic counter"（RL，噪声事件特征）和 §5 的 "categorical cyclic filter"（监督，带指针校正）是不同模型，主 claim 却把它们并成"结构应放在信念更新上"。需要在 §3 或 §5 用一句话说明二者在结构上的共同点：类别分布加循环平移。

**M4（DA）新颖性。** Prop 1 是已知构造（已标 cf.）。论文的贡献在于经验分解，Journal-Fit 视角会追问 "so what"。引言需要一句话说明这个分解对实践者的含义，例如：在一个循环任务里，什么情况下应该把预算花在估计器上，什么情况下花在 planner 上。

## MINOR

1. fig_rl 图例中仍有 "Born (ours)"；Transformer 标注 5.9k 参数，Table S2 / S5 为 20k，需要核对配置后重画。生成脚本缺失，只能从原始日志重建。
2. fig_phase (a) 的 x 轴范围 [-1, 1] 有大约一半是空白，有数据的话建议把范围收到 [-0.6, 0.9]。
3. Table 1 的 "Median crossing steps" 用了 M 单位混写（15.744M / .256M），建议统一保留两位有效数字。
4. 附录仍有若干历史表（Table S5，born_char 实现缺失）。建议只保留与主 claim 相关的部分，并把来源问题集中写在一个 provenance 段里。
5. 摘要 160 词以内可以再压一句，例如把 valve 结果并入前一句。

## 写作（Writing Quality Check）

- 合并稿中 which / what / when / where / whose 定语从句已经清零。
- 段落内术语一致："cyclic filter"、"rotor"、"phase rotation" 的指代都已统一，不存在同义词轮换。
- 仍可改进：§5.2 第三段有两个转折（"yet…", "Calibration also…"），可以拆成两段。
