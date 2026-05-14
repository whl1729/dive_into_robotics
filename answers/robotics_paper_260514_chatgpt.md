机器人的发展史，本质上是：

```text
机械 → 控制 → 感知 → 规划 → 自主 → 智能体（Agent）
```

的发展过程。

如果把机器人领域的发展划分阶段，大致可以分为：

| 阶段          | 核心突破                           |
| ----------- | ------------------------------ |
| 1940s–1960s | 控制论 / 工业机器人                    |
| 1960s–1980s | AI Robotics / 移动机器人            |
| 1980s–2000s | 行为机器人 / SLAM                   |
| 2000s–2015  | Humanoid / ROS / 无人系统          |
| 2015–2022   | Deep Learning Robotics         |
| 2023–2026   | Foundation Model / Embodied AI |

下面我按“机器人技术演进路线”介绍最具代表性的论文与项目。

---

# 一、控制论与机器人思想起源（1940s–1960s）

现代机器人最早来自：

```text
Cybernetics（控制论）
```

---

## 1. Cybernetics（控制论）

### 论文/著作

《Cybernetics: Or Control and Communication in the Animal and the Machine》

---

### 作者

Norbert Wiener

---

### 核心贡献

提出：

```text
反馈控制（Feedback Control）
```

即：

```text
感知 → 决策 → 动作 → 再感知
```

这是所有机器人系统的核心闭环。

---

### 行业影响

现代机器人中的：

* PID 控制
* 自动驾驶
* 无人机
* 强化学习

本质都继承自控制论思想。

---

# 二、工业机器人时代（1950s–1970s）

---

## 2. Unimate（第一台工业机器人）

### 历史意义

1959 年，世界第一台工业机器人：

```text
Unimate
```

进入 General Motors 工厂。

---

### 作者/发明者

* George Devol
* Joseph Engelberger

---

### 核心贡献

第一次实现：

```text
机器人替代流水线工人
```

主要用于：

* 焊接
* 搬运
* 压铸

---

### 行业影响

开启：

```text
工业自动化时代
```

今天：

* ABB
* FANUC
* KUKA

本质上都是 Unimate 的后代。

---

# 三、AI Robotics 时代（1960s–1980s）

机器人开始：

```text
具备“思考能力”
```

---

## 3. Shakey（第一个智能移动机器人）

### 论文/项目

[Shakey - Computer History Museum](https://www.computerhistory.org/revolution/artificial-intelligence-robotics/13/289%7C?utm_source=chatgpt.com)

---

### 作者

来自：

* SRI International

核心研究者：

* Nils Nilsson
* Charles Rosen

---

### 核心贡献

Shakey 是世界上第一个：

```text
能够自主规划的移动机器人
```

它可以：

* 感知环境
* 建立地图
* 路径规划
* 推理任务
* 执行动作

---

### 为什么重要？

Shakey 首次把：

```text
AI + 机器人
```

真正结合。 ([CHM][1])

---

### 更深远的影响

Shakey 项目直接催生：

* A* 搜索算法
* 路径规划
* Computer Vision
* Task Planning

等多个 AI 分支。 ([维基百科][2])

---

# 四、行为机器人（Behavior-Based Robotics）

这是机器人史上一次巨大“路线转向”。

---

## 4. Rodney Brooks 的行为机器人

### 代表论文

《A Robust Layered Control System for a Mobile Robot》

---

### 作者

Rodney Brooks

---

### 核心思想

Brooks 反对：

```text
机器人必须先建完整世界模型
```

他提出：

```text
感知直接驱动动作
```

即：

```text
Behavior-Based Robotics
```

---

### 核心架构

```text
Subsumption Architecture
```

---

### 为什么革命性？

此前机器人：

```text
先思考，再行动
```

Brooks：

```text
先行动，再适应
```

---

### 行业影响

这是：

```text
Embodied AI
```

的思想起点。

今天：

* 强化学习机器人
* 四足机器人
* 端到端机器人

都深受 Brooks 影响。 ([encyclopedia.com][3])

---

# 五、SLAM 时代（1990s–2015）

SLAM 是现代移动机器人最核心技术之一。

---

## 5. SLAM（同步定位与建图）

### 综述论文

《Past, Present, and Future of Simultaneous Localization And Mapping》

[SLAM Survey（arXiv）](https://arxiv.org/abs/1606.05830?utm_source=chatgpt.com)

---

### 作者

包括：

* Davide Scaramuzza
* John J. Leonard

---

### 核心问题

机器人需要同时：

* 知道自己在哪
* 建立环境地图

即：

```text
Localization + Mapping
```

---

### 为什么重要？

SLAM 是：

* 自动驾驶
* 无人机
* 扫地机器人
* AR/VR

的基础。

---

### 行业影响

SLAM 让机器人真正走向：

```text
自主导航
```

时代。 ([arXiv][4])

---

# 六、人形机器人时代（2000s）

---

## 6. ASIMO（Honda）

### 项目

ASIMO

---

### 公司

Honda

---

### 核心突破

ASIMO 首次实现：

* 稳定双足行走
* 上下楼梯
* 人机交互
* 跑步

---

### 为什么重要？

它证明：

```text
Humanoid Robot 可行
```

---

### 行业影响

ASIMO 影响了：

* Atlas
* Optimus
* Figure AI

等后续人形机器人。 ([维基百科][5])

---

# 七、ROS 时代（2007–至今）

---

## 7. ROS（Robot Operating System）

### 论文

《Robot Operating System 2: Design, Architecture, and Uses In The Wild》

[ROS2 Paper（arXiv）](https://arxiv.org/abs/2211.07752?utm_source=chatgpt.com)

---

### 作者

包括：

* Brian Gerkey

---

### 核心贡献

ROS 提供：

```text
机器人 Linux
```

即：

* 消息通信
* 驱动
* 地图
* 导航
* 感知

统一框架。

---

### 行业影响

ROS 极大降低机器人开发门槛。

今天：

```text
绝大多数机器人实验室
```

都使用 ROS/ROS2。 ([arXiv][6])

---

# 八、深度学习机器人（2015–2022）

机器人开始：

```text
从“规则”转向“学习”
```

---

## 8. Google Robot Grasping

### 代表论文

《Learning Hand-Eye Coordination for Robotic Grasping》

---

### 作者

来自：

* Google

包括：

* Sergey Levine

---

### 核心贡献

机器人通过：

```text
自监督学习
```

学会抓取。

---

### 为什么重要？

以前：

```text
人工写规则
```

现在：

```text
机器人自己学
```

---

### 行业影响

开启：

```text
Deep RL Robotics
```

时代。

---

# 九、Embodied AI / Foundation Model（2023–2026）

这是机器人领域当前最大变革。

---

## 9. RT-2（Google DeepMind）

### 论文

《RT-2: Vision-Language-Action Models Transfer Web Knowledge to Robotic Control》

[RT-2 Paper（Google DeepMind）](https://robotics-transformer2.github.io/?utm_source=chatgpt.com)

---

### 作者

来自：

* Google DeepMind

---

### 核心思想

把机器人变成：

```text
Vision-Language-Action Model
```

即：

```text
LLM + Robot
```

---

### 关键突破

机器人可以：

* 理解自然语言
* 理解视觉
* 生成动作

例如：

```text
“把最像可乐的饮料递给我”
```

机器人能完成。

---

### 为什么革命性？

这是首次：

```text
互联网知识 → 机器人动作
```

真正打通。

---

### 行业影响

RT-2 标志：

```text
Embodied Foundation Model
```

时代开始。

这也是：

* Tesla Optimus
* Figure AI
* OpenAI Robotics
* NVIDIA GR00T

共同方向。

---

# 十、机器人发展路线总结

| 阶段                     | 核心特征             |
| ---------------------- | ---------------- |
| 工业机器人                  | 重复执行             |
| AI Robotics            | 推理规划             |
| Behavior Robotics      | 感知驱动动作           |
| SLAM                   | 自主导航             |
| Humanoid               | 人类环境适配           |
| Deep Learning Robotics | 数据驱动             |
| Embodied AI            | Foundation Model |
| Agentic Robotics       | 会推理的机器人          |

---

# 十一、当前最值得关注的方向

---

## 1. Embodied AI

机器人 + LLM。

代表：

* RT-2
* OpenVLA
* GR00T

---

## 2. 世界模型（World Model）

机器人开始：

```text
在脑中模拟未来
```

---

## 3. Sim2Real

仿真训练 → 现实部署。

---

## 4. 通用机器人基础模型

未来可能出现：

```text
机器人版 GPT
```

---

# 十二、推荐你的阅读顺序

建议按下面路线阅读：

## 第一阶段（历史基础）

1. Cybernetics
2. Shakey
3. Brooks Behavior Robotics

---

## 第二阶段（现代机器人）

4. SLAM Survey
5. ROS2
6. ASIMO

---

## 第三阶段（AI Robotics）

7. Deep RL Robotics
8. RT-2
9. OpenVLA

---

# 十三、非常值得读的综述

---

## 机器人历史综述

[Dissecting Robotics（arXiv）](https://arxiv.org/abs/1704.08617?utm_source=chatgpt.com)

系统介绍机器人发展史。 ([arXiv][7])

---

## 群体机器人综述

[Swarm Robotics Survey（arXiv）](https://arxiv.org/abs/2101.00671?utm_source=chatgpt.com)

介绍：

* 蜂群机器人
* 多机器人协作
* 分布式智能

等方向。 ([arXiv][8])

[1]: https://www.computerhistory.org/revolution/artificial-intelligence-robotics/13/289%7C?utm_source=chatgpt.com "Shakey - CHM Revolution"
[2]: https://en.wikipedia.org/wiki/Shakey_the_robot?utm_source=chatgpt.com "Shakey the robot"
[3]: https://www.encyclopedia.com/science/encyclopedias-almanacs-transcripts-and-maps/brief-history-robotics-1950?utm_source=chatgpt.com "A Brief History of Robotics since 1950 | Encyclopedia.com"
[4]: https://arxiv.org/abs/1606.05830?utm_source=chatgpt.com "Past, Present, and Future of Simultaneous Localization And Mapping: Towards the Robust-Perception Age"
[5]: https://en.wikipedia.org/wiki/ASIMO?utm_source=chatgpt.com "ASIMO"
[6]: https://arxiv.org/abs/2211.07752?utm_source=chatgpt.com "Robot Operating System 2: Design, Architecture, and Uses In The Wild"
[7]: https://arxiv.org/abs/1704.08617?utm_source=chatgpt.com "Dissecting Robotics - historical overview and future perspectives"
[8]: https://arxiv.org/abs/2101.00671?utm_source=chatgpt.com "Past, Present, and Future of Swarm Robotics"
