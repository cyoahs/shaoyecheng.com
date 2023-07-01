---
title: cv
date: 2023-06-30 18:17:27
---

# 个人信息

- 姓名：邵烨程
- 邮箱：shaoyecheng@zju.edu.cn
- 研究方向：足式机器人运动控制 / 强化学习

# 教育背景

- 浙江大学 固体力学 博士 2018-2023
- 浙江大学 工程力学 学士 2014-2018

# 学术研究

## 基于动作生成的足式机器人运动控制 2022 - 2023

1. 以真实动物的动作捕获数据为基础，针对机器人的尺寸特点进行筛选和修正，设计动画状态机，生成可控的运动学动作。
2. 训练强化学习策略，将实时生成的动作作为观测量之一，模仿所生成的动作。所得控制策略可以实时跟随动作生成模型，并迁移到实物机器人。

## 视觉辅助下的四足机器人地形适应性研究 2021 - 2022
1. 根据落脚点的位置，生成能够适应不同地形的运动轨迹。在物理仿真引擎中训练强化学习策略，将落脚点位置作为观测量之一，模仿所生成的运动轨迹。
2. 所得策略在仿真测试中可以适应不同地形，在新地形下具备泛化能力，实物迁移尚在调试。

## 基于相位引导的四足机器人步态切换 2020 - 2021
1. 将不同的四足运动步态抽象成四只脚的独立周期运动，并用相位进行描述。将相位作为观测量之一，在仿真中训练强化学习策略，模仿不同相位所对应的步态。
2. 该方法将多任务学习简化为简单问题，可以使用简单的 LSTM 学会数十种步态，并泛化到未学过的步态。策略可以成功迁移到实物机器人。成果发表在 RAL 上，并被 ICRA 2022 录用。

## 基于模仿学习的四足机器人高速控制器（参与） 2017 - 2019
1. 使用模仿-驰豫两步的强化学习，训练高速奔跑的控制器，在 mini-cheetah 同尺寸的实物机器人上可以实现 5m/s 的高速运动。
2. 提出一种基于熵的稳定性判据，验证所得控制器的稳定性。论文发表在 Nature Machine Intelligence 上，本人为第三作者。

## 量子力学模拟揭示金刚石常温塑性行为微观机理 2019
1. 所在团队首次观测到金刚石在常温下的塑性变形行为，结合实验观察与文献资料，构建金刚石位错的原子结构模型。
2. 使用 NEB 方法，基于量子力学计算（DFTB），首次模拟出了金刚石位错滑移行为的原子机理。
3. 使用第一性原理（VASP）计算不同应变情况下金刚石的弹性属性，结合理论模型，预测了金刚石发生塑性变形的应变量，与实验观察相符。

## 基于机器学习的分子动力学模拟 2017 - 2018
1. 通过量子力学计算获取高精度的原子间相互作用数据库，训练机器学习模型，根据原子相对位置预测其能量与相互作用力。
2. 使用机器学习模型进行分子动力学模拟，计算精度优于经典模型，计算效率优于量子力学。

# 发表论文

- **SHAO Y**, JIN Y, LIU X, HE W, WANG H, YANG W. Learning Free Gait Transition for QuadrupedRobotsViaPhase-GuidedController[J/OL].IEEERoboticsandAutomationLetters, 2022, 7(2): 1230-1237. https://doi.org/10.1109/LRA.2021.3136645.
- **SHAO Y**, JIN Y, HUANG Z, WANG H, YANG W. A Learning-Based Control Framework
For Generic Motor Skills[J]. Journal of Zhejiang University-SCIENCE A. In Press. http://www.jzus.zju.edu.cn/iparticle.php?doi=10.1631/jzus.A2300128
- JIN Y, LIU X, **SHAO Y**, WANG H, YANG W. High-speed quadrupedal locomotion by imitation-relaxation reinforcement learning[J/OL]. Nature Machine Intelligence, 2022, 4(12): 1198-1208. https://doi.org/10.1038/s42256-022-00576-3.
- LIANG C, ZHANG Q, **SHAO Y**, BU Y, LIU J, LI X, WANG H, YANG W.Influenceofload orientations with respect to twin boundaries on the deformation behaviors of high-entropy alloy nanocrystals[J/OL]. MRS Bulletin, 2021, 46(3): 205-216. https://doi.org/10.1557/s43577-021-00046-y.
- NIE A, BU Y, HUANG J, **SHAO Y**, ZHANG Y, HU W, LIU J, WANG Y, XU B, LIU Z, WANG H, YANG W, TIAN Y. Direct Observation of Room-Temperature Dislocation Plasticity in Diamond[J/OL]. Matter, 2020, 2(5): 1222-1232. https://doi.org/10.1016/j.matt.2020.02.011.
- WANG P, **SHAO Y** , WANG H, YANG W. Accurate interatomic force field for molecular dynamics simulation by hybridizing classical and machine learning potentials[J/OL]. Extreme Mechanics Letters, 2018, 24: 1-5. https://doi.org/10.1016/J.EML.2018.08.002.