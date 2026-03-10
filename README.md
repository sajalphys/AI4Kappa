# AI4Kappa

[English](README.md) | [简体中文](README.zh-CN.md)

AI4Kappa is a Streamlit application for estimating lattice thermal conductivity (`κL`) directly from crystal structures in CIF format. The codebase combines CGCNN-based elastic-property inference with two downstream calculation routes:

- `KappaP`: a Slack-style physical model workflow.
- `PINK`: a physics-informed workflow that uses predicted elastic quantities and a simplified analytical formula.
- `Custom Kappa`: a page for user-supplied bulk modulus, shear modulus, and optional Gruneisen parameter.

The public deployment is available at [https://kappap-ai.streamlit.app/](https://kappap-ai.streamlit.app/).

## What The Code Actually Does

After a CIF file is uploaded, the application:

1. Converts the uploaded structure to a primitive representation and stores it in [`root_dir`](./root_dir).
2. Builds `id_prop.csv` for CGCNN inference.
3. Loads the two pretrained models in [`model`](./model) one by one to predict:
   - bulk modulus
   - shear modulus
4. Reconstructs crystal descriptors from the primitive structure:
   - number of atoms
   - density
   - volume
   - total atomic mass
5. Computes acoustic quantities from the predicted or user-provided elastic parameters:
   - longitudinal and transverse sound velocity
   - average sound velocity
   - Poisson ratio
   - acoustic Debye temperature
   - Gruneisen parameter
6. Evaluates `κL` with either the KappaP route or the PINK route at `300 K`.

This behavior is implemented across [`app.py`](./app.py), [`Pages/KappaP.py`](./Pages/KappaP.py), [`Pages/PINK.py`](./Pages/PINK.py), [`Pages/CustomKappa.py`](./Pages/CustomKappa.py), [`streamlit_scripts/file_op.py`](./streamlit_scripts/file_op.py), [`streamlit_scripts/calculate_K.py`](./streamlit_scripts/calculate_K.py), and [`predict.py`](./predict.py).

## Included Pages

- `Home`: overview, equations, certificate preview, and contact information.
- `KappaP`: predicts elastic properties from CIF and evaluates the Slack-style expression.
- `PINK`: predicts elastic properties from CIF and evaluates the physics-informed expression.
- `Custom Kappa`: lets the user enter elastic parameters manually for up to 5 structures.

## Repository Layout

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

Important assets:

- [`model/`](./model): pretrained checkpoints for bulk and shear modulus prediction.
- [`root_dir/atom_init.json`](./root_dir/atom_init.json): atom embeddings required by CGCNN.
- [`JMI_Supporting_Information/`](./JMI_Supporting_Information) and [`KappaP_Supporting_Information/`](./KappaP_Supporting_Information): tutorial videos and screening result tables distributed with the project.

## Installation

The current project is configured around Python `3.9`.

```bash
git clone --depth=1 https://github.com/Jack-Liu0227/AI4Kappa.git
cd AI4Kappa
python -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

If you prefer Conda:

```bash
conda create -n ai4kappa python=3.9
conda activate ai4kappa
python -m pip install -r requirements.txt
```

## Run Locally

```bash
streamlit run app.py
```

Then open the local Streamlit URL shown in the terminal and upload one or more CIF files from the sidebar.

## Inputs And Outputs

Input:

- `.cif` crystal structure files

Displayed outputs depend on the page, but the tables are built from the following code-level quantities:

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
- `Kappa_Slack (W m-1 K-1)` or `Kappa_cal (W m-1 K-1)`

## Method Notes

- The CGCNN predictor is used here for inference only; training code is not exposed through the Streamlit UI.
- The app currently evaluates thermal conductivity at `300 K` in [`streamlit_scripts/calculate_K.py`](./streamlit_scripts/calculate_K.py).
- `Custom Kappa` accepts at most 5 CIF files in one run.
- Uploaded files are rewritten to primitive structures before most downstream calculations.

## Citation

If you use AI4Kappa or the PINK workflow in academic work, cite the paper below in addition to any method-specific references relevant to your study.

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

## Contact

- Prof. Zhibin Gao: `zhibin.gao@xjtu.edu.cn`
- Yujie Liu: `liu_yujie@stu.xjtu.edu.cn`
