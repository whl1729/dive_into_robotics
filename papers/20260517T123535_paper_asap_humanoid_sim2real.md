---
title: paper_asap_humanoid_sim2real
date: 2026-05-17 Sun 12:35
identifier: 20260517T123535
source: https://arxiv.org/abs/2502.01143 (RSS 2025)
authors: Tairan He, Jiawei Gao, Wenli Xiao, Yuanhang Zhang, Zi Wang, Jiashun Wang, Zhengyi Luo, Guanqi He, Nikhil Sobanbab, Chaoyi Pan, Zeji Yi, Guannan Qu, Kris Kitani, Jessica Hodgins, Linxi "Jim" Fan, Yuke Zhu, Changliu Liu, Guanya Shi
venue: RSS 2025 (CMU + NVIDIA)
---

# 问题

机器人能在仿真里把 Cristiano Ronaldo 的 180 度转体跳、LeBron 的 "Silencer"、Kobe 的后仰跳投跳得有模有样——只要你有一个仿真器、一台 GPU、一点 RL 调参的耐心。可一旦把这套学好的策略搬到真的 Unitree G1 身上，它直接摔在地上。

为什么？仿真的物理永远不等于真世界的物理。仿真里假定电机响应是理想的，关节摩擦是常数，连杆质量是图纸上的数字。真世界里，电机会过热、摩擦随磨损变化、有 backlash、有 cable 牵拽、有不在模型里的连杆耦合。

这事大家不是没试过解决：

- *SysID*：拿真机器人测电机参数、连杆质量、摩擦系数，写回仿真。问题：你猜不到的那个变量校不到。而且常常需要扭矩传感器，很多机器人没这个。
- *Domain Randomization*：训练时把仿真参数随机化几千遍。问题：训出来的策略变保守——它学到的是"无论参数怎么变都不出错"，那就只敢慢慢动。敏捷动作直接没了。
- *学一个真实动力学模型 f_real(s,a)*：在无人机和无人车上 work。但人形机器人 23+ 自由度、动作空间大、采数据成本极高，没人走通。

ASAP 给了一个完全不同的答案。它没去修仿真，没去学真动力学，而是问：**有没有可能让那个不准的仿真原封不动，但在策略和仿真之间塞一个翻译器，让仿真的反应看起来像真世界？**

# 翻译

把 sim-to-real 想成"在练习钢琴上练出来的曲子，怎么搬到音乐厅那台真钢琴上"。

练习钢琴走音了。SysID 是请调音师调每根琴弦——能调一些但调不全。DR 是给你 100 台不同走音方式的钢琴轮流练，希望练出一种"啥琴上都能弹"的肌肉记忆——结果是没人敢弹快段。

ASAP 的做法：在你按键的手指和琴键之间装一个**机械小盒子**。你按下中央 C，盒子悄悄把手指推到偏右那个键上，于是走音的琴弹出来的是真正的 C。你练的时候带着这个盒子练——这台走音琴 + 盒子 ≈ 一台正常调的钢琴。等到你上音乐厅那台真钢琴，**盒子摘掉**，因为真钢琴本身是准的，不需要骗。手指动作不变，曲子不变，发出的是对的音。

这个"机械小盒子"在论文里叫 *delta action model*，写作 π^Δ(s, a)。它接收当前状态 s 和策略原本想出的动作 a，输出一个微调量 Δa。仿真器实际收到的是 a + Δa。

## 怎么训这个翻译器

走三步：

1. *先在仿真里训一个粗策略*——让它学会跟踪人体动作（输入是从视频 retarget 出来的关节轨迹）。这一步不在乎 sim-real 差距，先在仿真里跑通基础跟踪就行。
2. *把这个粗策略丢到真机上跑*。每个时刻记三件事：状态 s_t、策略发出的动作 a_t、真实下一步状态 s^real_{t+1}。
3. *回到仿真重放这些动作*。如果仿真和真实物理一样，仿真重放出的下一步状态应该等于真实下一步。但它们不等。差距就是训练信号——用 RL 训 π^Δ，让它输出 Δa 使 f^sim(s, a + Δa) ≈ f^real(s, a)。奖励是状态匹配 + Δa 幅度小（不能让翻译器乱改）。

## 翻译器装好后怎么用

关键操作：**把翻译器装回仿真**，冻住它的参数，重新精调原策略。

```
原始：     策略 -> a    -> 仿真(假物理)         -> 偏离真实的状态
带翻译器:  策略 -> a    -> π^Delta(s,a) -> a+da -> 仿真(假物理) -> ~ 真实下一步
```

精调过程中，"假物理 + 翻译器"行为上等价于"真实物理"。策略学出来的就是适配真实物理的策略。

部署到真机时——**翻译器扔掉**。它的存在意义是骗仿真器；真世界本身就是真世界，骗谁？直接把精调后的策略跑在真机上。

## 一处让人眼前一亮的可视化

Fig 13 把学出来的 π^Δ 在 23 个关节上的平均输出幅度画出来：

```
              upper body              lower body
shoulder/elbow ~0.014                hip pitch ~0.038
wrist (估算)   ~0.013                knee     ~0.049
                                     ankle pitch ~0.054
                                     ankle roll  ~0.022
```

下半身关节的修正量明显比上半身大，膝和踝最猛。原因不复杂——下半身要承重要发力，电机离极限近，"假设理想响应"和真实响应的偏差最大。这件事写在论文里只是一句话，但它等于说：**真实和仿真的差距不是均匀分布的**——它有结构。SysID 用统一参数空间抓不到这种结构，DR 用均匀噪声抓不到，DeltaDynamics 在状态空间硬学很容易过拟合。Action-space residual 学的就是这种结构。

## 效果

三类迁移测试：

- *IsaacGym → IsaacSim*（sim-to-sim 1）：Hard 难度下闭环 MPJPE，SysID 165 mm，DeltaDynamics 137 mm，**ASAP 129 mm**
- *IsaacGym → Genesis*（sim-to-sim 2）：Hard 难度下，SysID 186，DeltaDynamics 190，**ASAP 129**
- *IsaacGym → Real (Unitree G1)*：踢腿任务 MPJPE 从 vanilla 的 61.2 降到 50.2；OOD 的 LeBron Silencer 从 159 降到 112。论文的开头那六组炫技动作——Ronaldo 转体跳、Kobe 跳投、1.5m 前跳、1.3m 侧跳——baseline 全做不了，ASAP 都能稳定执行。

值得单独提的对比是 *DeltaDynamics*——直接在状态空间学 f^Δ(s,a) 修正下一状态。开环（重放动作）时它表现不错，但闭环时误差越累越大，最终发散：Fig 5 那张图很直观，DeltaDynamics 的 MPJPE 曲线一路爬到 2000 mm，ASAP 一直平在 100 mm 以下。原因是状态空间维度高、易过拟合到训练轨迹分布；而 action space 维度更低、约束更紧、不容易这样炸。

# 核心概念

## Delta Action Model

*一句话*：一个 RL 学出来的小补丁，挂在策略和仿真器中间，让仿真器的"行为"对得上真实世界的"行为"——而不是让仿真器的"参数"对得上真实世界的"参数"。

*类比*：就是上面说的钢琴小盒子。SysID 是调每根弦，DR 是练 100 台烂琴，Delta Action 是手指和琴键之间装个翻译。

*为什么重要*：少了它整篇论文就只是"先在 sim 训一遍再 fine-tune 一下"——和已有方法没区别。π^Δ 是真正起作用的 sim-to-real 桥梁，论文其他部分都是绕着它转的工程支架。

## 三种修正空间的对比（论文真正的认知贡献）

|     | 在哪里加补丁 | 学什么 | 维度 | 失败模式 |
|-----|------------|--------|------|---------|
| SysID | 仿真参数 | 物理参数估计 | 低 | 参数空间预设漏掉的部分抓不到 |
| DeltaDynamics | 状态转移函数 | 下一状态修正 Δs | 高 | 闭环过拟合、误差累积 |
| ASAP | 动作 | 动作修正量 Δa | 中 | 数据需求大（23-DoF 训不动） |

三者行为意义上等价——都是让仿真表现像真实——但训练难度和泛化性差异巨大。这个对比是论文真正的认知贡献，比"我们提出了一个新方法"重要。

## 不对称 Actor-Critic + 相位条件

Critic 看到一切（含真值速度、参考动作的全局位姿），Actor 只看到本体感觉 + 一个相位变量 φ ∈ [0,1]。Critic 帮训练，Actor 部署时不依赖外部状态估计——这意味着真机上**不用 odometry**。少了它真机部署得装一套定位系统，对硬件要求陡然抬升。这是工程层面让 ASAP 真能落地的小但关键的设计。

# 洞见

**当你修不好世界，就修你跟世界说话的方式。**

把 sim-to-real 看成控制问题而不是建模问题——这是 ASAP 真正的认知翻转。SysID 想知道"真实物理是什么"。DeltaDynamics 想学"真实状态怎么变"。ASAP 不问这两个，只问："要让我和真实的差距消失，我应该把命令改成什么样？"

这个翻转的杠杆点是 action space。它是策略唯一能控制的通道，是把所有 unmodeled 物理因素的影响汇聚到的最低维出口。修这个通道，能借四两之力压住千斤的物理 gap。

这思路不止用在仿真。任何"主体不变 + 加一层薄补丁"的设计——LoRA、residual networks、prompt engineering——都是同一套哲学的不同变体：别动昂贵的主体，在接口处加一个低成本可学的薄层。

# 博导审稿

学生你这篇拿过来，我先翻视频——OK，Cristiano 转体跳、LeBron Silencer 在真 G1 上能做出来，硬证据有了。

*选题*：sim-to-real 是这两年人形机器人最痛的问题之一，问题真实、benchmark 真实、不是造出来的。Strong。

*方法*：核心想法不算全新——residual learning、residual action 之前都有人做过（你自己也引了 RGAT, [35]）。你的贡献是把它系统地放在 humanoid 这个 setting 下，跟 SysID/DR/DeltaDynamics 做了清楚的方法学对比，画出了"修正空间"这个分类。**这个对比比方法本身还有价值**。但要诚实点——RGAT 已经做过 residual action + learned forward dynamics，跟它的差异得在 related work 讲得更细。

*实验*：三 simulator 迁移 + 真机，ablation 覆盖 dataset size、training horizon、action norm，还做了 random action noise 的对比来证明"不只是噪声扰动"——这组 ablation 我满意。Fig 13 那张关节级 delta magnitude 可视化是亮点，让"非均匀 dynamics gap"这件事变成可看见的东西。但有几处不够：

- 真机只在 5 个动作上测，OOD 评估只有一个 LeBron Silencer。覆盖窄。
- 23-DoF 训不动只能降到 4-DoF ankle delta——这是大让步。等于说核心方法在最复杂场景下还没真正 scale 起来。
- 两个 G1 训坏了——这数据有趣但说明 pipeline 还很脆弱，离工业落地不近。

*写作*：Fig 2 总览图清楚，方法分阶段讲解结构清晰。但 Table IV closed-loop 评估那一堆数字密度太高，得反复看才能抓住 ASAP 在 Hard level 的优势。可视化能再帮读者一把。

*判决*：**strong accept**。不是因为方法多惊艳，而是因为问题 framing 清楚、对比公道、real-world 证据扎实，且开源了多 simulator 评估 codebase——对社区是真贡献。RSS 2025 中得合理。

# 启发

*迁移*：delta action 这套"在动作通道加薄补丁"的思路可以直接搬到其他"主体不变 + 环境差异适配"的场景。比如把同一个 LLM deploy 到客服 vs 法律助手两个领域：不重训主体，给每个领域学一个轻量 adapter（LoRA 思路）——本质就是 ASAP 的 LLM 版。这意味着你可以**先训一个昂贵的通用 base policy，然后给每个新部署环境只训一个便宜的 delta**，而不是每次都重新训整个策略。

*混搭*：SysID 和 ASAP 不互斥。先用 SysID 把那些有传感器能直接测的参数校到位（粗调），剩下的 unmodeled 部分让 delta action 兜底（细调）。这样对真机数据的需求会小很多，因为 delta action 只学剩余 gap 而不是全部 gap。论文没做这个组合实验，是值得追的方向。

*反转*：通常碰到部署效果差，第一反应是"重新训"。ASAP 的反思路是"加薄补丁"。这套思路的应用边界值得想：什么时候补丁优于重训？答案大概是——*主体训练成本极高 + 差异是低维结构性的*。在自动驾驶 BEV 模型从一个城市迁移到另一个城市的场景，可能"学一个跨城市 perception 修正层"比重训整个 BEV 更经济。这个直觉值得一个具体实验验证。
