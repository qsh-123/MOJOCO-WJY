# MuJoCo MPC 汽车仪表盘项目

- **代码仓库**: 
- **运行视频**: https://www.bilibili.com/video/BV1bWvrBDE1h/?spm_id_from=333.1387.list.card_archive.click&vd_source=a50a5a7677335f45bbbbf5fe2f52c9d1
- **作业报告**: 

## 项目信息
- **学号**: 232011082
- **姓名**: 万嘉宇
- **班级**: 计科2302班
- **完成日期**: 2025年12月28日

## 项目概述
本项目聚焦 MuJoCo MPC 在 VMware 虚拟机环境的适配与扩展，核心实现两大功能：
1. 完成自定义 SimpleCar 小车仿真任务的全流程集成，包括搭建小车物理模型（XML）、编写控制逻辑与奖励函数（C++）、配置编译脚本，解决官方无此任务导致的识别失败问题
2. 提供多套图形禁用方案（环境变量 / 无头参数），彻底规避 GLFW 初始化报错

项目支持无可视化后台运行，同时兼容官方内置任务（如 Cartpole、Walker），为虚拟机环境下的 MPC 仿真提供稳定、可扩展的解决方案。

## 环境要求
- **操作系统**: Ubuntu 24.04 LTS 
- **编译器**: gcc 13.3.0
- **CMake**: 3.28.1
- **其他依赖**:
  - 图形渲染：libgl1-mesa-dev, libglfw3-dev, libglew-dev
  - 数值计算：libeigen3-dev, libopenblas-dev
  - 构建工具：git, cmake, build-essential

## 项目目录
- `.gitignore` — 忽略 build/、*.o、VS/QT 缓存等临时文件
- `CMakeLists.txt` — 顶层构建脚本，自动拉取 MuJoCo/MJPC/Abseil 并生成可执行文件 mjpc
- `LICENSE` — MIT 开源许可证
- `README.md` — 项目简介与一键编译/运行指南
- `report.md` — 课程大作业报告（设计思路、测试结果、未来工作）

- `code/`
  - `app.cc` — 主入口：初始化 GLFW 窗口、调度仿真循环、捕获键盘事件
  - `app.h` — app.cc 的头文件：窗口/渲染上下文声明
  - `simple_car.cc` — SimpleCar 任务实现：加载 MJCF、残差计算、状态转移、数据接口
  - `simulate.cc` — 仪表盘数据提取与平滑滤波（供 simple_car.cc 调用）
  - `simulate.h` — simulate.cc 的接口声明 + DashboardData 结构体
  - `test_simple_car.cc` — 单元测试：离线验证车速、油耗、指针角度计算是否正确

- `photo/`
  - 截图*.png — 运行截图：仪表盘效果、速度曲线、警告状态

- `videos/`
  - *.mp4 — 演示录像：完整导航过程 + 3D 仪表盘实时变化

- `logs/`
  - `mujoco_log.log` — 最近一次构建输出：方便排查依赖缺失或编译错误
  - `debug.log` — 运行时调试信息：车速、转速、油耗逐帧数值

## 编译和运行

### 编译步骤
```bash
cd /path/to/your/mujoco_mpc
cmake .
make -j$(nproc)
```

### 运行
```bash
./bin/mjpc --task=SimpleCar
```

## 功能说明

### 已实现功能
- [x] 速度表：显示车辆当前速度，范围0-200 km/h，指针随速度变化
- [x] 转速表：模拟发动机转速，范围0-8000 RPM，指针随速度模拟值变化
- [x] 数字显示：在右下角以进度条形式显示油量（百分比）和温度（摄氏度）
- [x] 数据实时更新：仪表盘数据每帧从MuJoCo仿真中提取并刷新，确保与车辆状态同步

### 进阶功能
- [x] UI动画效果：仪表盘指针采用平滑过渡动画，避免突兀的跳动
- [x] 警告提示：当模拟转速超过6000 RPM时，转速表背景会高亮红色区域，提供视觉警告

## 文件说明
- `car_model.xml`：汽车的物理场景/模型配置文件，定义了汽车的刚体结构、关节约束、物理参数等，是后续采集速度、转速等仪表盘数据的物理模型基础
- `README.md`：介绍“simple_car”任务的功能、使用方法、依赖要求等，是该任务的快速上手指南
- `simple_car.c`：该任务的核心功能实现代码，包含汽车的运动控制逻辑、状态数据的计算与输出等，是仪表盘数据的处理核心
- `simple_car.h`：声明simple_car.c中的函数、数据结构、宏定义等接口，供本文件或其他模块调用，是代码模块间的接口约定文件
- `task.xml`：定义“simple_car”任务的参数、目标、约束条件等，是该任务的运行规则配置文件

## 已知问题
- 控制输入缺失：车辆目前无有效驱动方式（如键盘或MPC控制器），只能通过MuJoCo UI手动拖拽，不利于仪表盘动态测试。计划在下一阶段接入mjpc自带的controller或添加GLFW键盘回调

## 参考资料
- MuJoCo 官方文档: https://mujoco.readthedocs.io/
- MuJoCo MPC GitHub 仓库: https://github.com/google-deepmind/mujoco_mpc
- LearnOpenGL CN 教程: https://learnopengl-cn.github.io/
- 《C++ Primer》(第5版) - Lippman, Lajoie, Moo
- 作业文档《大作业.md》
