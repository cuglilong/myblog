---
title: "SEIS_FILO中文手册"
subtitle: ""
date: 2022-12-08T09:43:30+08:00
draft: true
author: ""
authorLink: ""
description: ""
keywords: ""
license: ""
comment: false
weight: 0

tags:
- draft
categories:
- draft

hiddenFromHomePage: false
hiddenFromSearch: false

summary: ""
resources:
- name: featured-image
  src: featured-image.jpg
- name: featured-image-preview
  src: featured-image-preview.jpg

toc:
  enable: true
math:
  enable: true
lightgallery: false
seo:
  images: []

# See details front matter: /theme-documentation-content/#front-matter
---

<!--more-->

SEIS_FILO : 基于McMC(马尔可夫链蒙特卡洛)跨维贝叶斯方法联合反演面波频散和接收函数

## 一、BEFORE USE

### Overview

地震学研究利用平面和各向同性层的分层来近似地描述地球的结构。这种一维模型适用于单站测量，包括接收函数和面波频散曲线，已被广泛应用于从浅盆地到深部地幔结构的广泛范围。近年来，越来越多的研究开始使用更复杂的方法(如全波形反演)来模拟地球横向非均匀结构。然而，由于计算成本较低，且不需要密集的地震仪器部署，一维结构建模将继续保持其地位。特别是在海洋区域，由于台站网络覆盖不足，只能采用单台站的方法。

该程序包旨在对接收函数和面波频散曲线进行跨维反演，充分考虑海水柱的影响。最先进的贝叶斯概率抽样被称为可逆跳跃马尔可夫链蒙特卡洛，其中模型参数的数量(即层数)是根据数据复杂性自动选择的。同时还采用了平行回火技术来提高转化率。源代码是用现代的Fortran编程风格(即面向目标的编程)编写的，将来维护成本低且易于扩展。

### Requirements

必须安装以下库

- FFTW library

- LAPACK library

上述库的适当位置必须在src/Makefile中指定

- Open MPI

MPI编译器必须链接到GNU Fortran编译器(即gfortran)。这些程序已被确认可以使用GNU Fortran编译器4.8.5版本工作。旧版本可能无法编译程序，因为代码包含了现代Fortran特性。

Plot实用程序需要Python3和流行的模块，包括numpy、matplotlib、pandas和seaborn。有关所需模块的完整列表，请参阅requirements.txt。

### Install

- Use make in the src directory.

- Executable files will be created under the bin directory.

- mpifort is required.

- It is recommended that mpifort is liked to the GNU compiler (i.e., gfortran). Otherwise, some modifications to compiler options are required in src/Makefile.

### Terms of use

- Please clarify the URL of the GitHub repository (https://github.com/akuhara/SEIS_FILO) and the package name (SEIS_FILO) when you make any presentation or publish articles using this program. The example sentence is: This study uses a computer program SEIS_FILO, which is available at https://github.com/akuhara/SEIS_FILO.

- This program is licensed under the GNU General Public License v3.0.

##  二、HOW TO USE

### 1 Input files

###### 1.1 Overview of input files

Input files to SEIS_FILO's programs are categorized into three classes: (1) main parameter file, (2) data summary file, and (3) data file. The first one, the main parameter file, is a mandatory input to all programs (i.e., recv_func_fwd, disper_fwd, and joint_inv) and the filename must be provided as the first command-line argument. The user's choice of tuning parameters should be stated in this file. The other ones, the data summary file and the data file, are only required for inversion (i.e., joint_inv). The data summary file contains metadata of the input dataset and must be prepared separately for each receiver function and surface wave datasets. The filenames are passed to the program through the parameters recv_func_in and disper_in, which should appear in the main file. The data files store the input data. The filenames should appear in the data summary file.

###### 1.2 Main parameter file

The main parameter file contains user's choice on tuning parameters. This filename must be passed to SEIS_FILO's programs as the first command-line argument. See parameter list for descriptions of each parameter.

**EXAMPLE**

```shell
recv_func_fwd [path to parameter file]
disper_fwd [path to parameter file]
mpirun -np 20 joint_inv [path to parameter file]
```

**Format**

- Specify one parameter per line
- Parameter's name should be on the left side of "=" and the value on the right side
- Comment out by "#"
- Order insensitive
- Empty lines are just ignored

```shell
# Snippet of the parameter file
n_iter = 100000
n_chain = 20
# The following lines give the same results.
n_burn = 50000
n_ bu rn= 500 0 0
```

**Required parameters for disper_fwd**

- freq_or_period: dispersion measurement type (freq: dispersion curve as a function of frequency; period: as a function of period)
- xmin: minimum frequecny or period of dispersion curve (Hz or s)
- xmax: maximum frequency or period of dispersion curve (Hz or s)
- dx: freqeuncy or period interval of dispersion curve (Hz or s)
- cmin: minimum phase velocity for root search (km/s)
- cmax: maximum phase velocity for root search (km/s)
- dc: inverval of phase velocity (km/s)
- disper_phase: phase type (R: Rayleigh, L: Love)
- n_mode: mode number (n_mode >= 0)
- vmod_in: filename for input velocity model
- disper_out: output filename

NOTE

```txt
disper_phase = L (Love wave) is nut supported for current version.
When n_mode < 0, full search mode is acctivated
```

**Required parameters for recv_func_fwd**

- n_smp: number of samples in receiver function data
- delta: time interval of receiver function (s)
- rayp: ray parameter (s/km)
- a_gauss: Gaussian low-pass filter parameter
- t_pre: time before direct P arrival in receiver function (s)
- rf_phase: phase type (P: P-wave, S: S-wave)
- deconv_flag: whether deconvolved by Z component or not (T: with deconvolution, F: without deconvolution)
- correct_amp: amplitude correction to account for energy loss by filtering (T: with correction, F: without correction)
- damp: water-level damping factor
- vmod_in: filename for input velocity model
- recv_func_out: output filename

**Required parameters for joint_inv**

- recv_func_in : filename of receiver function data summary file
- disper_in: filename of dispersion curve data summary file
- solve_anomaly: solve for velocity anomaly (T) or aboslute velocity (F)
- ref_vmod_in: Filename of reference velocity model
- solve_vp: whether solve for Vp (T: solve Vp, F: not)
- solve_rf_sig: whether solve receiver function data error (T) or not (F)
- solve_disper_sig: whether solve dispersion curve data error (T) or not (F)
- is_sphere: Earth flattening is applied (T) or not (F).
- is_ocean: whether model includes ocean (T: with ocean layer, F: without ocean layer)
- ocean_thick: ocean layer thickness (only required when is_ocean=T)
- vp_bottom: Vp of bottom layer (km/s).
- vs_bottom: Vs of bottom layer (km/s).
- rho_bottom: Density of bottom layer (g/cm^3).
- n_iter: number of iterations
- n_burn: number of iterations for burn-in period
- n_corr: number of iterations each time before saving a model
- n_chain: number of MCMC chains per process
- n_cool: number of non-tempered MCMC chains per process
- temp_high: maximum temperature
- k_min: minimum number of layer interfaces
- k_max: maximum number of layer interfaces

- vp_min: minimum Vp (km/s)

- vp_max: maximum Vp (km/s)
- dvp_sig: Prior width for Vp anomaly (km/s)
- vs_min: minimum Vs (km/s).
- vs_max: maximum Vs (km/s).
- dvs_sig: Prior width for Vs anomaly (km/s)
- z_min: minimum interface depth (km)
- z_max: maximum interface depth (km)
- dev_vs: standard deviation of proposed Vs perturbation (km/s)
- dev_vp: standard deviation of proposed Vp perturbation (only required when solve_vp=T) (km/s).
- dev_z: standard deviation of proposed layer depth perturbation (km).
- n_bin_vs: number of Vs bin for display.
- n_bin_vp: number of Vp bin for display
- n_bin_z: number of depth bin for display.

###### 1.3 Data summary file

The data summary file passes the summary of input data to the inversion program joint_inv. Different file formats are used for surface wave dispersion curves and receiver functions. The filename can be arbitrary, which are to be specified in main parameter file via the parameters disper_in and recv_func_in.

**Surface wave dispersion curves**

Format

- Comment out by # is acceptable

- Empty lines are ignored.

- Line 1: n_disp (The number of data files)

- Line 2 and after: Summary of each data file (must be repeated n_disp times)

  - Line i: data file name

  - Line ii: disper_phase, n_mode, freq_or_period

  - Line iii: nx, xmin, dx

  - Line iv: cmin, cmax, dc

  - Line v: sig_c_min, sig_c_max, dev_sig_c

  - Line vi: sig_u_min, sig_u_max, dev_sig_u

  - Line vii: sig_hv_min, sig_hv_max, dev_sig_hv

**Receiver functions**

Format

- Comment out by # is acceptable

- Empty lines are ignored.

- Line 1: n_rf (The number of data files)

- Line 2 and after: Summary of each data file (must be repeated n_rf times)

  - Line i: data file name

  - Line ii: a_gauss, rayp, delta

  - Line iii: rf_phase

  - Line iv: t_start (start time of analysis window), t_end (end time of the analysis window)

  - Line v: sig_rf_min, sig_rf_max, dev_sig_rf

  - Line vi: deconv_flag, correct_amp, damp

###### 1.4 Data file

The data file contains the input data for inversion. Two different formats are used for surface wave dispersion curves and receiver functions. The file name and detailed information about the data should appear in the [data summary files]().

**Surface wave dispersion curves**

FORMAT

1st col.	2nd col.	3rd col.	4th col.	5th col.	6th col.
Phase velocity (km/s)	Data availability (T or F)	Group velocity (km/s)	Data availability (T or F)	H/V ratio	Data availability (T or F)

NOTE

- Ascending order is assumed in terms of frequency/period (the first line must contain measurements at xmin).
- Example is here.

**Receiver functions**

- Use [SAC data file format]() 

###### 1.5 Reference velocity file

When solving for velocity anomalies (solve_anomaly = T), you must pass a reference velocity model through this file. The filename can be arbitrary but needs to be specified in a main parameter file using the parameter v_ref_mod_in.

Format

1st column	2nd column	3rd column
Depth (km)	Vp (km/s)	Vs (km/s)

An example is [here]().

NOTE

- DEPTH INCREMENTS MUST BE CONSTANT.

- The part shallower than the seafloor is ignored. The inversion fixes the Vp of the ocean layer at value of the parameter vp_ocean (1.5 km/s by default).

###### 1.6 Velocity model file

This file describes the velocity model that is passed to the forward computation programs (i.e., disper_fwd and recv_func_fwd).

Format

- Line 1: the number of layers (including seawater and half-space).
- Line 2 and after: Vp (km/s), Vs (km/s), density (g/cm^3), thickness (km)

NOTE

- For the ocean layer, set Vs to be lower than 0 (this is allowed only for the topmost layer).

- The thickness of the bottom layer (half-space) is not used.

- Empty lines are ignored.

- Comment out by "#" works fine.

- Example is [here]().

###### 1.7 Parameter list 

SEIS_FILO's programs involve a number of tuning parameters. The complete list is given here. These parameters should appear in the main parameter file or data summary file.

### 2 Output files

###### 2.1 Number of layers

Filename: n_layers.ppd

Format:

1st column	2nd column
Number of layers	Probability

###### 2.2 Velocity profile

Filename: vs_z.ppd

Format:

1st column	2nd column	3rd column
S wave velocity (km/s)	Depth (km)	Probability
Filename: vp_z.ppd

Format:

1st column	2nd column	3rd column
P wave velocity (km/s)	Depth (km)	Probability
Filename: vs_z.mean

Format:

1st column	2nd column
Mean S wave velocity (km/s)	Depth (km)
Filename: vp_z.mean

Format:

1st column	2nd column
Mean P wave velocity (km/s)	Depth (km)

###### 2.3 Dispersion Curve Ensemble

###### 2.4 Receiver function ensemble

###### 2.5 Standard deviation of noise

### 3 Plot utilitites

### 4 Forwarad calculation

###### 4.1 Dispersion curve

###### 4.2 Receiver functions

### 5 Inversion analysis

###### 5.1 Model parameters

###### 5.2 Prior porbability

###### 5.3 McMC step

###### 5.4 Prallel tempering

### 6 Miscellaneous

###### 6.1 Receiver function amplitude

###### 6.2 Verification

###### 6.3 Spherical Earth mode

###### 6.4 Diagnostic mode


