# AI4Kappa

[English](README.md) | [简体中文](README.zh-CN.md)

AI4Kappa 是一个基于 Streamlit 的晶格热导率 (`κL`) 计算与预测应用，面向 CIF 晶体结构文件。当前代码实现将 CGCNN 弹性性质预测与两条下游计算路线结合起来：

- `KappaP`：基于 Slack 思路的物理模型流程。
- `PINK`：基于物理启发公式的流程，使用预测得到的弹性量进行 `κL` 计算。
- `Custom Kappa`：允许用户手动输入体模量、剪切模量和可选的 Gruneisen 参数。

在线版本可访问：[https://kappap-ai.streamlit.app/](https://kappap-ai.streamlit.app/)。

## 代码实际完成的流程

上传 CIF 文件后，程序会按如下顺序执行：

1. 将上传结构转换为 primitive structure，并写入 [`root_dir`](./root_dir)。
2. 生成供 CGCNN 推理使用的 `id_prop.csv`。
3. 依次加载 [`model`](./model) 中的两个预训练模型，预测：
   - 体模量
   - 剪切模量
4. 从 primitive structure 中提取晶体描述符：
   - 原子数
   - 密度
   - 体积
   - 总原子质量
5. 基于预测值或用户输入值，计算声学与热学相关量：
   - 横波声速
   - 纵波声速
   - 平均声速
   - 泊松比
   - 声学德拜温度
   - Gruneisen 参数
6. 在 `300 K` 下，使用 KappaP 或 PINK 路线计算 `κL`。

上述流程可在 [`app.py`](./app.py)、[`Pages/KappaP.py`](./Pages/KappaP.py)、[`Pages/PINK.py`](./Pages/PINK.py)、[`Pages/CustomKappa.py`](./Pages/CustomKappa.py)、[`streamlit_scripts/file_op.py`](./streamlit_scripts/file_op.py)、[`streamlit_scripts/calculate_K.py`](./streamlit_scripts/calculate_K.py) 和 [`predict.py`](./predict.py) 中直接对应到实现。

## 页面说明

- `Home`：功能概览、公式展示、软件证书预览和联系方式。
- `KappaP`：从 CIF 预测弹性参数，并按 Slack 路线计算热导率。
- `PINK`：从 CIF 预测弹性参数，并按物理启发公式计算热导率。
- `Custom Kappa`：允许最多 5 个结构手动输入参数并计算结果。

## 仓库结构

```text
AI4Kappa/
|-- app.py
|-- multipage.py
|-- predict.py
|-- Pages/
|-- cgcnn/
|-- model/
|-- root_dir/
|-- streamlit_scripts/
|-- style/
|-- JMI_Supporting_Information/
`-- KappaP_Supporting_Information/
```

关键资源：

- [`model/`](./model)：体模量与剪切模量预测模型权重。
- [`root_dir/atom_init.json`](./root_dir/atom_init.json)：CGCNN 所需原子嵌入初始化文件。
- [`JMI_Supporting_Information/`](./JMI_Supporting_Information) 与 [`KappaP_Supporting_Information/`](./KappaP_Supporting_Information)：项目附带的教程视频与筛选结果表。

## 安装

当前项目文档建议使用 Python `3.9`。

```bash
git clone --depth=1 https://github.com/Jack-Liu0227/AI4Kappa.git
cd AI4Kappa
python -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

如果使用 Conda：

```bash
conda create -n ai4kappa python=3.9
conda activate ai4kappa
python -m pip install -r requirements.txt
```

## 本地运行

```bash
streamlit run app.py
```

启动后，在终端显示的本地 Streamlit 地址中打开页面，并在左侧上传一个或多个 CIF 文件。

## 输入与输出

输入：

- `.cif` 晶体结构文件

页面展示结果取决于具体模块，但底层计算表主要围绕以下字段构建：

- `Number of Atoms`
- `Density (g cm-3)`
- `Volume (A^3)`
- `the total atomic mass (amu)`
- `Bulk modulus (GPa)`
- `Shear modulus (GPa)`
- `Sound velocity of the transverse wave (m s-1)`
- `Sound velocity of the longitude wave (m s-1)`
- `Speed of sound (m s-1)`
- `Poisson ratio`
- `Gruneisen parameter`
- `Acoustic Debye Temperature (K)`
- `Kappa_Slack (W m-1 K-1)` 或 `Kappa_cal (W m-1 K-1)`

## 方法说明

- 当前仓库中的 CGCNN 只在应用里用于推理，未提供训练界面。
- 应用在 [`streamlit_scripts/calculate_K.py`](./streamlit_scripts/calculate_K.py) 中将计算温度固定为 `300 K`。
- `Custom Kappa` 页面单次最多支持 5 个 CIF 文件。
- 上传后的结构会先尽量转换为 primitive structure，再进入后续计算流程。

## 引用

如果你在学术工作中使用了 AI4Kappa 或 PINK 工作流，建议在方法学引用中加入下述论文，并结合你的具体研究补充相应模型来源文献。

```bibtex
@Article{jmi.2024.86,
  AUTHOR = {Yujie Liu and Xiaoying Wang and Yuzhou Hao and Xuejie Li and Jun Sun and Turab Lookman and Xiangdong Ding and Zhibin Gao},
  TITLE = {PINK: physical-informed machine learning for lattice thermal conductivity},
  JOURNAL = {Journal of Materials Informatics},
  VOLUME = {5},
  YEAR = {2025},
  NUMBER = {1},
  ARTICLE-NUMBER = {12},
  URL = {https://www.oaepublish.com/articles/jmi.2024.86},
  ISSN = {2770-372X},
  DOI = {10.20517/jmi.2024.86}
}
```

## 联系方式

- 高致彬教授：`zhibin.gao@xjtu.edu.cn`
- 刘宇杰：`liu_yujie@stu.xjtu.edu.cn`
