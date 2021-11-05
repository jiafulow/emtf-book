# Usage

## Overview

The Phase-2 EMTF++ emulator is completely decoupled from the Phase-1 EMTF emulator, so that they can evolve independently. The EMTF++ emulator has very basic functionality and is not capable of doing everything that the EMTF emulator does. It is expected that more features will need to be added in the future.

**EMTF++ emulator structure** &mdash; It behaves as a wrapper around the HLS source code. The wrapper part handles the typical CMSSW stuff (e.g. input and output objects, event setup, etc). The core is the HLS source code that implements the actual algorithm.

**Modifying HLS source code** &mdash; HLS stuff is being developed in a separate repository outside of CMSSW. It follows the setup of a Vivado HLS project, which allows one to perform FW synthesis and implementation. Once satisfied, the HLS source code is then imported into the emulator.

Note that the HLS FW is not complete yet. Notably, it is missing: the interface to the trigger primitives, the coordinate conversion, the displaced muon capabilities, and the demands from the downstream (i.e. GMT).

**Private ntuples** &mdash; The various studies are usually done with private ntuples. Basically, a ntupler is used to extract the EMTF++ emulator output and store them in a simple ROOT TTree.


## How-to Guide

### How to check out the EMTF++ emulator code?

### How to run the EMTF++ emulator code?

### How to make private ntuples?

### How to make private ntuples with CRAB3?

### How to check out the HLS source code?

### How to run the HLS source code?

### How to import the HLS source code into the emulator?
