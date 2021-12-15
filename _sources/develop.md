# Develop

## Overview

This section describes how to generate the patterns, how to train the NN, and how to include the updates into the HLS source code and the emulator code. It also describes how to make the rate and efficiency plots.

Most of the tasks here are done with Python + Jupyter. A Conda environment is used to manage the Python packages. NumPy is used extensively. The ML framework of choice is TensorFlow. The tasks are executed by running the Jupyter notebooks.

If you are not familiar with Jupyter, please look at:

- [Get Started - Jupyter Documentation](https://jupyter.readthedocs.io/en/latest/content-quickstart.html)

If you are not familiar with Conda, please look at:

- [Getting started with conda](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html)

If you are not familiar with NumPy, please look at:

- [NumPy: the absolute basics for beginners](https://numpy.org/doc/stable/user/absolute_beginners.html)

If you are not familiar with TensorFlow, please look at:

- [Tutorials | TensorFlow Core](https://www.tensorflow.org/tutorials)

## How-to Guide

### How to convert the private ntuples into NumPy arrays?

Before doing any of the tasks here, the private ntuples need to be converted into NumPy arrays. This step has dependency on ROOT, and thus CMSSW; but after the conversion, you will no longer have dependency on ROOT and CMSSW.

First, follow the set up instructions as used for [How to make private ntuples](usage.html#how-to-make-private-ntuples).

Then, change directory:

``` bash
cd $CMSSW_BASE/src/L1TMuonSimulations/Analyzers/workspace/
```

The location of the ntuples are hardcoded in the script `emtf_ntuples.py`. Look at `SingleMuon` for particle gun ntuples, and `SingleNeutrinoPU200` for neutrino PU200 ntuples. You should modify them if necessary.

Then, in the script `ristretto.py`, select `analysis = 'signal'` if running on particle gun ntuples (default), or select `analysis = 'bkgnd'` if running on neutrino PU200 ntuples. Then, run it:

``` bash
python ristretto.py
```

### How to set up the Conda environment?

For the rest of the tasks, it's best to avoid any CMSSW dependency. A Conda environment is used instead. See [Getting started with conda](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html) to figure out how to install Conda.

First, create the following YAML file `conda-tf-environment.yml`:

::: {dropdown} Content of `conda-tf-environment.yml`

``` yaml
name: tf
channels:
  - defaults
dependencies:
  - more-itertools
  - jupyterlab
  - jupyter
  - seaborn
  - python=3.9
  - numpy
  - pyyaml
  - matplotlib
  - scipy
  - h5py
  - dask
  - pandas
```

:::

Then, create the Conda environment with the selected Python packages using the YAML file:

``` bash
conda update --yes -n base conda
conda env create --file conda-tf-environment.yml
```

Then, install TensorFlow via pip:

``` bash
conda activate tf
pip install -U pip
pip install -U tensorflow=2.6.0
pip install -U tensorflow-model-optimization=0.7.0
```

For the sake of consistency, it is recommeded to stay with TensorFlow `v2.6.0` and TensorFlow Model Optimization Toolkit `v0.7.0` rather than using the latest version.

Finally, install this EMTF++ Python package (unpublished):

``` bash
git clone git@github.com:jiafulow/emtf-nnet.git
cd emtf-nnet
pip install -U -e .
```

### How to get the Jupyter notebooks?

The notebooks are stored in this repository:

- <https://github.com/jiafulow/user-notebooks/tree/L1MuonTrigger-P2_11_1_7>

Please refer to `README.md` in the repo for details.

### How to generate the patterns?

In the Notebooks directory, execute the notebooks: `10-patterns-zone0.ipynb`, `11-patterns-zone1.ipynb`, and `12-patterns-zone2.ipynb`. Set the variables `signal_fname` and `bkgnd_fname` in these notebooks to be the locations of the NumPy arrays (converted from the private ntuples).

They will produce: `patterns_zone0.npz`, `patterns_zone1.npz`, and `patterns_zone2.npz`, respectively.

### How to train the NN?

In the Notebooks directory, execute the notebooks: `21-features-full.ipynb`. Set the variables `signal_fname` and `bkgnd_fname` in these notebooks to be the locations of the NumPy arrays (converted from the private ntuples).
It will produce `features.h5`, which is used as input to the next step.

Then, execute the notebooks: `22-predictions.ipynb` and `23-predictions-quant.ipynb`. Set the variable `features_fname` in these notebooks to be the location of the output file from the previous step. The difference between the two notebooks is that the first notebook does not apply NN quantization, while the second notebook does.

The first notebook will produce `nnet_model.json` and `nnet_model_weights.h5`; the second notebook will produce `quant_nnet_model.json` and `quant_nnet_model_weights.h5`.

### How to import the patterns and the NN into the HLS source code and the emulator code?

In the Notebooks directory, execute the notebook: `20-features-quick.ipynb`. Make some edits in the notebook:

- Set the variables `signal_fname` and `bkgnd_fname` to be the locations of the NumPy arrays (converted from the private ntuples).
- Set the variable `patterns_fname` to be the location of the patterns file (e.g. `patterns_zone0.npz`).
- Set the variable `nnet_model_fname` to be the location of the NN model file (e.g. `quant_nnet_model.json`)

The notebook will produce several JSON files:

- `pattern_bank.json`
- `nnet_weights.json`
- `x_test_sparse.json`
- `extracted_zonemerging_0.json`
- `extracted_trkbuilding_0.json`
- `extracted_duperemoval_0.json`
- `extracted_trainfilter_0.json`
- `extracted_fullyconnect_0.json`

Change directory to the working area for HLS source code. See [How to set up the HLS source code](usage.html#how-to-set-up-the-hls-source-code).

``` bash
cd <hls-working-area>
```

Then, copy the above files to the directory `scripts`. The scripts in this directory can be used to automatically write HLS source code and test bench files:

- Do `python make_pattern_bank.py` to generate the header file `pattern_bank.h`. Move it to the directory `firmware/emtf_hlslib/`.
- Do `python make_nnet_weights.py` to generate the header file `nnet_weights.h`. Move it to the directory `firmware/emtf_hlslib/`.
- Do `python make_testbench.py` to generate all the test bench files. Move them to the appropriate locations by following the instruction shown by the script.

Check that the import is successful by running on of the Tcl scripts.

### How to make rate and efficiency plots?

(This workflow is kind of tedious at the moment.)

If the patterns or the NN are updated, the emulator code should be updated and re-compiled.

Run the emulator and make the private ntuples for neutrino PU200 dataset (used for rate estimation) and muon PU200 dataset (used for efficiency estimation). See [How to make private ntuples](usage.html#how-to-make-private-ntuples).

Convert the private ntuples into NumPy arrays by using the script `espresso.py`. In the script `espresso.py`, select `analysis = 'rates'` if running on neutrino PU200 ntuples (default), or `analysis = 'effie'` if running on muon PU200 ntuples. Then, run it:

``` bash
python espresso.py
```

Go to the Notebooks directory, execute the notebooks: `30-rates.ipynb` and `31-efficiency.ipynb`. Set the variable `rates_fname` and `effie_fname` in these notebooks to be the location of the NumPy arrays.
