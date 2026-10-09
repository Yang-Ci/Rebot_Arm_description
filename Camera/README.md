# B601-RS 腕部相机描述

本目录提供 RealSense D405、RealSense D435i 和 Orbbec Gemini 2 三套相机装配 URDF，包含相机本体、支架、安装位置、安装螺钉参考系及光学坐标系。资源与 `ReBot_Arm_DigitalTwin_RS` 中已验证的版本一致。

## 目录结构

```text
Camera/
├── README.md
├── urdf/
│   ├── d405.urdf
│   ├── d435i.urdf
│   └── gemini2.urdf
├── meshes/
│   ├── mounts/                 # 两种安装支架
│   ├── realsense/              # D405 STL 和 D435 Collada
│   └── gemini2/                # Gemini 2 STL
├── source/                    # 上游装配和厂家 Xacro
├── source.json                # 来源版本、原始路径和文件哈希
├── licenses/
└── scripts/
    └── build-wrist-camera-urdfs.py
```

## 使用方式

三个 URDF 都以空的 `gripper_end` link 为根，表示机械臂腕部的安装参考系。机械臂本体见 [`../RS/urdf/ReBot_Arm_RS.urdf`](../RS/urdf/ReBot_Arm_RS.urdf)。Web 加载器可以把相机根节点挂到机械臂现有的同名 link 下。

合并成完整机械臂 URDF 时，删除相机 URDF 中作为占位的空 `gripper_end` link，再合入其余 link、joint 和 material，避免重复定义。合并到其他目录时，需要同步调整网格路径。

网格路径相对于 `urdf/` 目录，复制时请保留整个 `Camera/` 目录。ROS / RViz 使用时，可将路径转换为所在 ROS package 的 `package://` URI，并安装所有资源。D435i 使用 D435 的 Collada（`.dae`）外壳，加载器需要支持该格式。

加载已生成的 URDF 不需要厂家描述包、Xacro 或联网。三套描述共用部分 link 名称，使用时选择其中一套。

## 安装参数

下表保留上游装配 Xacro 的值。XYZ 单位为米，RPY 单位为弧度。支架坐标相对于 `gripper_end`，相机坐标相对于支架 link；D405、D435i 的相机位置指底部安装螺钉参考系，Gemini 2 的相机位置指 `camera_link`。

| 型号 | 支架 XYZ | 支架 RPY | 相机 XYZ | 相机 RPY |
| --- | --- | --- | --- | --- |
| D405 | -0.1201 0.0003 0.0507 | 1.5827 0.0024 1.5515 | -0.0003 0.0443 -0.0093 | -0.0225 -1.0448 -1.5460 |
| D435i | -0.1201 0.0003 0.0450 | 1.5827 0.0024 1.5515 | -0.0003 0.0445 -0.0095 | 0.0126 -1.0489 -1.5981 |
| Gemini 2 | -0.1201 0.0003 0.0450 | 1.5827 0.0024 1.5515 | 0.0249 0.0487 0.0090 | 0.0042 -1.0502 -1.5718 |

## 来源与许可

- 装配 Xacro 和转换后的支架 STL：[`xiehuangbao888/rebot_visual_grasp`](https://github.com/xiehuangbao888/rebot_visual_grasp/tree/dd28d65598deec767cf95fa45521d69b38155833/description)，提交 `dd28d65598deec767cf95fa45521d69b38155833`，包声明 Apache-2.0。
- 支架 CAD 来源：[`Yang-Ci/Camera-Mounts`](https://github.com/Yang-Ci/Camera-Mounts)，该仓库注明 B601 支架设计来自 Seeed-Projects/reBot-DevArm，采用 CERN-OHL-W-2.0。这里的 STL 是 `rebot_visual_grasp` 提供的转换版本。
- RealSense Xacro 和相机网格：[`realsenseai/realsense-ros`](https://github.com/realsenseai/realsense-ros/tree/9215f26e8348ad5922a608b77882a9bfe05940f0/realsense2_description)，提交 `9215f26e8348ad5922a608b77882a9bfe05940f0`，Apache-2.0。
- Gemini 2 Xacro 和网格：[`orbbec/OrbbecSDK_ROS2`](https://github.com/orbbec/OrbbecSDK_ROS2/tree/c153462518ad674650bafa4464fda72c27ab797a/orbbec_description)，提交 `c153462518ad674650bafa4464fda72c27ab797a`，Apache-2.0。

本次资源从 [`ReBot_Arm_DigitalTwin_RS`](https://github.com/Yang-Ci/ReBot_Arm_DigitalTwin_RS/tree/8e153cb248e30ae2f82275f26ec3f94768852784/reBotArm_simulator-RS/public/models/wrist-cameras) 的提交 `8e153cb248e30ae2f82275f26ec3f94768852784` 同步。许可文本位于 `licenses/`；`source.json` 记录原始路径、版本、字节数和 SHA-256。`source/` 和 `meshes/` 保留上游原始文件内容。

## 重新生成 URDF

重新生成时需要 Python `xacro` 模块。在本仓库根目录执行：

```bash
python3 Camera/scripts/build-wrist-camera-urdfs.py
```

脚本只使用本目录附带的源文件，将上游机械臂 include 替换为空的安装参考 link，展开相机宏，并把网格 package URI 转换为相对路径。
