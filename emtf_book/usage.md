# Usage

## Overview

The Phase-2 EMTF++ emulator is completely decoupled from the Phase-1 EMTF emulator, so that they can evolve independently. The EMTF++ emulator has very basic functionality and is not capable of doing everything that the EMTF emulator does. It is expected that more features will need to be added to the EMTF++ emulator in the future.

**EMTF++ emulator structure** &mdash; It behaves as a wrapper around the HLS source code. The wrapper part handles the typical CMSSW stuff (e.g. input and output objects, event setup, etc). The core is the HLS source code that implements the actual algorithm.

**Modifying HLS source code** &mdash; HLS stuff is being developed in a separate repository outside of CMSSW. It follows the setup of a Xilinx Vivado HLS project, which allows one to perform FW synthesis and implementation. Once satisfied, the HLS source code is then imported into the emulator.

Note that the HLS FW is incomplete at the moment. Notably, it is missing: the interface to the trigger primitives, the coordinate conversion, the displaced muon capabilities, and the demands from the downstream (i.e. GMT).

**Private ntuples** &mdash; Various studies are done with private ntuples. Basically, a "ntupler" is used to extract the EMTF++ emulator output and store them in a simple ROOT TTree. Doing this will save a lot of disk space as well as CPU time (as I/O is reduced).

If you are not familiar with ROOT, please look at:

- [Get Started - ROOT](https://root.cern/get_started/)

If you are not familiar with CMSSW, please look at:

- [The CMS Offline WorkBook](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBook)

If you are not familiar with Vivado HLS, please look at:

- [Vivado Design Suite User Guide: High-Level Synthesis (v2020.1)](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_1/ug902-vivado-high-level-synthesis.pdf)


## How-to Guide

### Prerequisites

- You must have access to a machine with CMSSW installed.
- You should have some experience with the CMSSW environment.
- You should be comfortable with Bash and Git.
- For the HLS stuff, you should have some experience with the HDL and HLS languages.

### How to set up the EMTF++ emulator code?

The emulator is based on `CMSSW_11_1_7`. The emulator code is put in `L1Trigger/Phase2L1EMTF/`, and the data format code is put in `DataFormats/L1TMuonPhase2/`.

The code repositories are:

- <https://github.com/jiafulow/Phase2L1EMTF-Jun2021>
- <https://github.com/jiafulow/L1TMuonPhase2DataFormats-Jun2021>

Set up instructions:

``` bash
source /cvmfs/cms.cern.ch/cmsset_default.sh
export SCRAM_ARCH=slc7_amd64_gcc820
cmsrel CMSSW_11_1_7
cd CMSSW_11_1_7/src
cmsenv
# Checkout repos
git clone git@github.com:jiafulow/Phase2L1EMTF-Jun2021.git L1Trigger/Phase2L1EMTF/
git clone git@github.com:jiafulow/L1TMuonPhase2DataFormats-Jun2021.git DataFormats/L1TMuonPhase2/
# Modify hls.xml to ignore certain warnings
sed -i 's@\(.*use name.*\)@\1\n  <flags CXXFLAGS="-Wno-int-in-bool-context -Wno-uninitialized -Wno-maybe-uninitialized"/>@' ../config/toolbox/slc7_amd64_gcc820/tools/selected/hls.xml
# Compile
scram b -j8
```

### How to run the EMTF++ emulator code?

To run the emulator, append the following lines in a cfg file (e.g. `my_cfg.py`):

``` python
# Create phase2L1EMTFSequence and include it in L1simulation_step
process.load('L1Trigger.Phase2L1EMTF.simCscTriggerPrimitiveDigisForEMTF_cfi')
process.load('L1Trigger.Phase2L1EMTF.rpcRecHitsForEMTF_cfi')
process.load('L1Trigger.Phase2L1EMTF.phase2L1EMTFProducer_cfi')
process.phase2L1EMTFSequence = cms.Sequence(process.simCscTriggerPrimitiveDigisForEMTF+process.rpcRecHitsForEMTF+process.phase2L1EMTFProducer)
process.L1simulation_step += process.phase2L1EMTFSequence
```

Then:

``` bash
cmsRun my_cfg.py
```

### How to make private ntuples?

There is an additional repository with the ntupler and other utilities:

- <https://github.com/jiafulow/L1TMuonSimulations-Jun2021>

Set up instructions (after the emulator has been set up):

``` bash
# You should be in $CMSSW_BASE/src/
git clone git@github.com:jiafulow/L1TMuonSimulations-Jun2021.git L1TMuonSimulations/
scram b -j 8
```

To generate single muon particle gun ntuples (using the parameters from the `Phase2HLTTDRSummer20` MC campaign):

``` bash
# You should be in $CMSSW_BASE/src/L1TMuonSimulations/Analyzers/test/
cmsRun pset_SingleMuon_PosEnd_2GeV_Phase2HLTTDRSummer20.py
```

To generate neutrino PU200 ntuples (also `Phase2HLTTDRSummer20`):

``` bash
# You should be in $CMSSW_BASE/src/L1TMuonSimulations/Analyzers/test/
cmsRun pset_SingleNeutrino_PU200_Phase2HLTTDRSummer20.py
```

### How to make private ntuples with CRAB3?

If you already have your own CRAB3 setup, you can just use it. If you don't, you can find my setup in this repository:

- <https://github.com/jiafulow/user-crab3/tree/L1MuonTrigger-P2_11_1_7>

Please refer to `README.md` in the repo for details.

### How to set up the HLS source code?

::: {note}
It is recommended that you separate the HLS environment from the CMSSW environment. It is also recommended that you use the specific version of Vivado HLS `v2020.1`.
:::

Set up instructions:

``` bash
# The following line depends on where Vivado HLS was installed.
source /data/Xilinx/Vivado/2020.1/settings64.sh
# Checkout repo
git clone git@github.com:jiafulow/emtf-hlslib.git
cd emtf-hlslib/
```

### How to run the HLS source code?

The Vivado HLS tool can be called by using Tcl scripts. For VU13P, do:

``` bash
vivado_hls -f run_hls_xcvu13p.tcl
```

For KU15P, do:

``` bash
vivado_hls -f run_hls_xcku15p.tcl
```

By default, the project area is reset and the following steps are executed: C Simulation, C Synthesis, C/RTL Co-simulation, RTL Synthesis and Implementation.

To use the Vivado HLS tool in GUI mode, simply call `vivado_hls` (or `vivado_hls -nosplash`).

### How to import the HLS source code into the emulator?

To import the HLS source code into the emulator, do:

``` bash
cd scripts/cmssw/
# The following Python script will create a directory called 'emtf_hlslib'
# which contains rewritten HLS source code.
python3 rewrite.py
# Replace '$CMSSW_BASE/src/L1Trigger/Phase2L1EMTF/src/emtf_hlslib/'
rm -rf $CMSSW_BASE/src/L1Trigger/Phase2L1EMTF/src/emtf_hlslib/
mv emtf_hlslib $CMSSW_BASE/src/L1Trigger/Phase2L1EMTF/src/
```

Also, to comply with the CMSSW coding style, you should call `scram b code-format` before compiling it.
