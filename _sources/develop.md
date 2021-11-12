# Develop

## Overview

This section describes how to generate the patterns, how to train the NN, and how to include the updates into the HLS source code and the emulator code. It also describes how to make the rate and efficiency plots.

Most of the tasks here are done with Python + Jupyter. A Conda environment is used to manage the Python packages. NumPy is used extensively. The ML framework of choice is TensorFlow.

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

Then, in the script `ristretto.py`, select `analysis = 'signal'` if you want to run on particle gun ntuples (default), and select `analysis = 'bkgnd'` if you want to run on neutrino PU200 ntuples. Then, run the script:

``` bash
python ristretto.py
```

### How to set up the Conda environment?

For the rest of the tasks, it's best to avoid any CMSSW dependency. So, a Conda environment is used.

Once you have Conda installed, do the following to create a Conda environment:

!TODO


### How to generate the patterns?

### How to train the NN?

### How to import the patterns and the NN into the HLS source code and the emulator code?

### How to make rate and efficiency plots?
