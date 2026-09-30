# 本车运动原语（state lattice）

## 文件

| 文件 | 说明 |
|---|---|
| `omni_10cm.json` | 本车用的运动原语。**`motion_model: omni`、`grid_resolution: 0.1`** |
| `omni_10cm.config.json` | 生成它用的配置，原样保存以便复现 |

## 为什么不用 `/opt/ros/.../sample_primitives/`

那是 **Nav2 自带的演示文件**，`grid_resolution = 0.05`，与本场代价图
（`maps/战队.yaml` → `resolution: 0.100000`）**对不上**。

Nav2 官方 `lattice_primitives/README.md` 原话：

> **grid_resolution** … **This should be the same as your costmap resolution.**

⇒ 拿官方 demo 硬套，原语几何比例差一倍。

## 怎么复现

```bash
# 1. 拉生成器（与机器人 Nav2 同版本：jazzy）
git clone --depth 1 --filter=blob:none --sparse -b jazzy \
  https://github.com/ros-navigation/navigation2 /tmp/nav2
cd /tmp/nav2 && git sparse-checkout set nav2_smac_planner/lattice_primitives

# 2. 依赖：numpy / matplotlib / Rtree
#    （无 root 时可用纯 Python 替身，见生成记录）

# 3. 生成
cd /tmp/nav2/nav2_smac_planner/lattice_primitives
python3 generate_motion_primitives.py \
  --config <本目录>/omni_10cm.config.json \
  --output <本目录>/omni_10cm.json
```

## 用法

`config/motion.yaml` 里：

```yaml
    GridBased:
      plugin: "nav2_smac_planner::SmacPlannerLattice"
      lattice_filepath: "/rmcs_install/rmcs-navigation/share/rmcs-navigation/lattice/omni_10cm.json"
```
