class: middle, center, title-slide
count: false

# The HEP Software Ecosystem

.huge.blue[Matthew Feickert]<br>
.huge[(University of Wisconsin-Madison)]

.large[
[APS Global Physics Summit](https://summit.aps.org/events/APR-P88/2)<br>
Advances in Computing in High Energy Physics Mini-Symposium<br>
March 18th, 2026
]

<!--
# Abstract

Particle physics relies on an expansive ecosystem of software tools to accomplish all aspects of the work needed to deliver physics results.
These range from tools required for the acquisition and handling of data in experiments, to pattern recognition and feature extraction (both in real time and in offline processing), to sophisticated analysis techniques.
These are complemented by tools for simulation of both the underlying physics and the interactions of particles in detectors, as well as tools to support data and workflow management.
Further software tools support the larger information system for particle physics needed to interpret and combine results across the global scientific community.

Components of this ecosystem have their own software lifecycle, though tools may live and evolve for decades, encapsulating significant intellectual effort and products from the HEP community with substantial physics impact.
This talk will review the global HEP software ecosystem and discuss how it is used in running experiments today, as well as how planned experiments are already building on, and contributing to, the foundation provided by the ecosystem to deliver scientific progress in particle physics in the coming decade and beyond.
-->

---
# Overview

.kol-3-5[
.larger[
* Robust and performant software is .bold[critical] to the high energy physics community
* Reflect on the scope of the HEP software community
   - Current software
   - Ongoing work to support experiments
   - Community support for future experiment software ecosystems
* We'll start by following the data generating physics processes to cover the breadth
]
]
.kol-2-5.center[
<div class="figure-vcenter" style="--top:40%;">
   <figure>
      <a href="https://cds.cern.ch/record/2802918">
         <img src="figures/HL-LHC-cpu-projections-atlas_annotated.png" width="100%">
      </a>
      <figcaption>Experiments given software community<br><b>new challenges and opportunities</b>.<br>Projected required compute usage for HL-LHC <br>(want R&D below budget line)</figcaption>
   </figure>
</div>
]

---
# Data Acquisition & Trigger

.kol-1-2[
.huge[
* Triggers
]
]
.kol-1-2.center[
<div class="figure-vcenter" style="--top:40%;">
   <figure>
      <a href="https://lhcb-starterkit-run3.docs.cern.ch/first-analysis-steps/dataflow/">
         <img src="figures/lhcb_online_dataflow.png" width="100%">
      </a>
      <figcaption>LHCb's "Allen" GPU HLT1 and "Moore" CPU HLT2 with the Moore software bring trigger rate to 30 MHz</figcaption>
   </figure>
</div>
]

<!-- LHC information from Chris Burr:
The highest level starting point we have is: https://lhcb-starterkit-run3.docs.cern.ch/first-analysis-steps/dataflow/

"Allen" is this "GPU HLT1" step in the second box on the left.
Moore is the "CPU HLT2" on the right of the buffer.
The 10GB/s is being sent from point 8 to the grid.
Which then leads to the offline processing section at the bottom of the page.
"Moore" (the software) is then used to do a process we call "sprucing".
Depending on the stream it's either:
* Just a file format conversion (TURBO)
* Running the an additional set of selections on top of the HLT2 selections (CALIB/FULL)
Everything up to this point is "central productions" which are done for people (which analysts only providing selections).
Then "Analysis Productions" typically involves running "DaVinci" to produce ROOT files of TTrees (soon to be ntuples) of candidates from the output of sprucing.
We can also run other embarrassingly parallel processing in analysis productions e.g.
* applying some ROOT RDataFrame workflow to every output file of DaVinci
* Use uproot and XGBoost to apply a BDT
* Run various calibration tools to add/transform columns
This is just user provided code running in conda environments. The only constraint is that it can be ran on any subset of input files and then have the output merged. People also run analysis productions on the output of analysis productions, and there is a ton of re-use/metadata management. Everything is logged, reproducible, metadata and application logs are persisted forever, if output data is used for a paper we archive it to tape for long term preservation and all that kind of stuff.
The analysis productions system reads about half an exabyte a year of input data.
(Analysis Productions is my main project in LHCb)
After analysis productions we aim to have processing fit on a single (potentially large) machine and then LHCb is very unopinionated. The ROOT files we write out doesn't have any custom types so you don't the LHCb physics software or dictionaries to read them.
Snakemake is pretty popular but there is a long list of things people use, a ton of fitting frameworks, some people use ROOT lots, some people use the purely pyhep stuff, most people use a mixture.
-->


---
# Event Reconstruction & Pattern Recognition

.kol-1-2[
.large[
* Track reconstruction, calorimeter clustering, particle flow
* *Acts* (A Common Tracking Software)
   - Cross-experiment tracking library
   - GPU-accelerated track finding
* ML-based reconstruction emerging
   - GNNs for tracking
   - ML-based jet calibration
]
]
.kol-1-2[
<div class="figure-placeholder">
   Recommended figure: Visualization of track reconstruction in a detector (e.g., Acts tracking display or event display showing reconstructed tracks)
</div>
]

.large[
Reconstruction is the **most CPU-intensive** step in the processing chain
]

???
Once events pass the trigger, they must be reconstructed — turning raw detector hits into physics objects like tracks, jets, and leptons. This is the most computationally expensive step. Track reconstruction alone can dominate the CPU budget, which has motivated cross-experiment efforts like Acts — A Common Tracking Software — that provides a detector-agnostic, thread-safe, GPU-capable tracking library. Machine learning is increasingly being explored for reconstruction tasks, with graph neural networks showing promise for track finding in the dense environments expected at the HL-LHC.


---
# Simulation

.kol-1-2[
.large[
* **Physics event generators**
   - MadGraph, Sherpa, Pythia, Herwig, EvtGen
   - Hard process → parton shower → hadronization → decay
* **Detector simulation**
   - Geant4: the cornerstone
* **Fast simulation** approaches
   - Parametric: AtlFast, Delphes
   - ML-based surrogates: FlashSim, CaloFlow
]
]
.kol-1-2[
<div class="figure-placeholder">
   Recommended figure: Diagram showing the simulation chain from generator through detector simulation, or comparison of full vs. fast simulation output
</div>
]

.large[
HSF Generator WG coordinates efforts to improve generator efficiency
]

???
Simulation is essential for interpreting experimental results, and it consumes roughly half of the global HEP computing budget. The chain starts with physics event generators — MadGraph and Sherpa for hard processes, Pythia and Herwig for parton showering and hadronization — and continues through Geant4 for detailed detector response simulation. Because full Geant4 simulation is so expensive, the community is actively pursuing fast simulation alternatives: parametric approaches like AtlFast and Delphes, and increasingly ML-based surrogates like CaloFlow for calorimeter simulation. The HSF Generator Working Group coordinates community-wide efforts to improve generator software efficiency, which is critical for the HL-LHC era.

---
# Flavour tagging

.kol-1-2[
.large[
* Flavor tagging software across HEP
]
]

---
# End-user Analysis

.kol-1-2[
.large[
* Show multilingual story here
* Comment on Jim timeline and evolution into 2020 - 2030s
]
]

---
# Analysis Reinterpretation

.kol-1-2[
.large[
* Rivet, RECAST
]
]

---
# Neutrino Physics specific

.kol-1-2[
.large[
* DUNE things
]
]

---
# Software for future experiments

.kol-2-3[
.large[
* In 2026 multiple .bold[future collider experiments] are exploring software studies:
   - Circular Electron Positron Collider (CEPC)
   - Compact Linear Collider (CLIC)
   - Electron-Ion Collider (EIC)
   - Future Circular Collider (FCC)
   - International Linear Collider (ILC)
   - Muon Collider
* [Key4HEP](https://github.com/key4hep) provides .bold[common] libraries and solutions for the generation, simulation, reconstruction, and analysis
]
]
.kol-1-3.center[
<div class="figure-vcenter" style="--top:40%;">
   <figure>
      <a href="https://key4hep.github.io/key4hep-doc/">
         <img src="figures/key4hep-logo.png" width="100%">
      </a>
   </figure>
</div>
]

---
# A look towards community software

* We should do it where we can

---
# Summary

* Outlook


---
class: end-slide, center

.large[Backup]

---

class: end-slide, center
count: false

The end.
