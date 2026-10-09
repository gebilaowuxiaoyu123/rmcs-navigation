# RMCS Navigation · 哨兵全局规划器改造

> 《2027 赛季算法组培训考核作业细则》§3 导航方向的**代码改动**仓。
> 提交报告与过程记录：https://github.com/gebilaowuxiaoyu123/RMCS_Navigation

本仓是 RMCS 导航系统代码，本次作业在其中做了以下改动。

## 改动

| # | 改了什么 | 位置 |
|---|---|---|
| 1 | 全局规划器改为 `SmacPlannerLattice`，并显式指定 `lattice_filepath` | `config/motion.yaml` |
| 2 | 自建 **10 cm 全向原语**（16 航向 / 136 条） | `lattice/omni_10cm.json` |
| 3 | 最后一轮调参：`inflation_radius` 0.28 → **0.38**、`PathAlignCritic.cost_weight` 5.2 → **7.5** | `config/motion.yaml` |
| 4 | 补上游缺失的 `rmcs_description` 依赖 | 包依赖 |

## 关键结论

- **全向底盘只能用 Lattice**：`SmacPlannerHybrid` 只支持阿克曼模型，用在全向车上等于凭空加转弯半径；
  而 Lattice 的运动能力由**原语文件**决定。
- **原语分辨率必须与代价图一致**：官方 demo 是 5 cm 阿克曼原语，本车代价图 0.1 m，故自建 10 cm 版。
- **Lattice 用默认 `change_penalty` 会给出穿墙路径**，需改 0.05 → 1.0。
- **膨胀半径必须大于车体外接圆**（0.364160），否则膨胀层失效；本车局部取 0.38。

## 复现

```
# 实车
sync-remote
ssh-remote "bash -lc 'source ~/env_setup.bash && ros2 param get /planner_server GridBased.plugin'"
attach-remote -r          # 退出用 Ctrl-A 再按 d，别按 Ctrl-C
```

## 分支与上游

- 本仓改动分支：`feat/nav-on-zlq8-5`
- 上游：`Alliance-Algorithm/rmcs-navigation`（remote 名 `upstream`，仅拉更新）
