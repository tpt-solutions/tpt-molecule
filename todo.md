# tpt-molecule TODO

Copyright (c) 2026 TPT Solutions. Dual-licensed MIT OR Apache-2.0.
Rule: every dependency MIT, BSD, Zlib, Unicode, ISC, or dual MIT/Apache-2.0. No Apache-2.0-only, GPL, LGPL, AGPL, MPL. No C/C++ FFI.

## Phase 0: Repo, Licensing & CI Scaffold
- [ ] 0.1 Rename folder `tpt-moledule` -> `tpt-molecule`
- [ ] 0.2 git init, .gitignore, default branch, initial commit
- [ ] 0.3 Cargo workspace root (resolver 2, shared package metadata, workspace.dependencies)
- [ ] 0.4 Scaffold crates: core, graph, dynamics, compute, cli, bindings/tpt-molecule-py
- [ ] 0.5 Pin MSRV and rust-toolchain.toml; set edition
- [ ] 0.6 LICENSE-MIT and LICENSE-APACHE (copyright 2026 TPT Solutions); `license = "MIT OR Apache-2.0"` in all manifests
- [ ] 0.7 README, CONTRIBUTING (dual-license contribution clause), SECURITY, CODE_OF_CONDUCT
- [ ] 0.8 deny.toml (current cargo-deny schema): allow MIT, BSD-2/3, Zlib, Unicode-3.0, ISC; Apache-2.0 NOT in allow; deny GPL/AGPL/LGPL/MPL
- [ ] 0.9 deny.toml bans: rdkit, openbabel, openmm, mdanalysis, bindgen-based chem crates, cust/cuda crates
- [ ] 0.10 Verify `cargo deny check` passes on empty workspace; document any per-crate exceptions with justification
- [ ] 0.11 Wire in tpt-math and tpt-simd (path/git/crates.io); confirm both pass cargo-deny
- [ ] 0.12 Audit tpt-math API: L-BFGS, conjugate gradient, linear algebra, no_std status
- [ ] 0.13 Audit tpt-simd API: popcount, vectorized Tanimoto, no_std status
- [ ] 0.14 CI (GitHub Actions): fmt, clippy -D warnings, test, cargo-deny, cargo-audit, docs build
- [ ] 0.15 CI matrix: Windows, Linux, macOS; stable + MSRV
- [ ] 0.16 CI: no_std check (thumbv7em target) for tpt-molecule-core
- [ ] 0.17 CI: wasm32-unknown-unknown build check for core, graph, compute
- [ ] 0.18 CI: `unsafe` policy (`#![forbid(unsafe_code)]` where possible; document exceptions)
- [ ] 0.19 Set up criterion benchmark harness crate/dir
- [ ] 0.20 Set up offline golden-fixture generator scripts (Python + RDKit/OpenMM, dev-time only, fixtures committed, no runtime dependency; keep out of build)
- [ ] 0.21 Architecture docs / ADR folder (ADR-001..003 from spec)
- [ ] 0.22 Reserve crate names on crates.io and project name on PyPI

## Phase 1: Core & Graph Engine (spec months 1-3)
### 1A tpt-molecule-core (no_std + alloc)
- [ ] 1.1 `#![no_std]` + alloc crate skeleton, feature flags (std, serde)
- [ ] 1.2 Element enum + periodic table (Z 1-118): symbol, name, group/period
- [ ] 1.3 Atomic masses, isotopes (mass, abundance)
- [ ] 1.4 Van der Waals and covalent radii
- [ ] 1.5 Electronegativity, valence tables, default valences
- [ ] 1.6 Physical constants (Boltzmann, Avogadro, Coulomb, etc.), source/CODATA year documented
- [ ] 1.7 Unit conversions (kJ/mol, kcal/mol, eV, Angstrom/nm, amu, fs/ps)
- [ ] 1.8 Tests: table integrity, symbol roundtrip, constant values
- [ ] 1.9 Verify builds on no_std target and wasm32
### 1B tpt-molecule-graph: model
- [ ] 1.10 Atom and Bond types (element, isotope, charge, H count, aromatic, chirality, atom map)
- [ ] 1.11 Molecule type on petgraph (atoms=nodes, bonds=edges)
- [ ] 1.12 Implicit hydrogen handling and valence calculation
- [ ] 1.13 Ring perception (SSSR) and ring info
- [ ] 1.14 Aromaticity perception model (document model choice vs RDKit)
- [ ] 1.15 Stereochemistry: tetrahedral chirality, double-bond E/Z, CIP labels
- [ ] 1.16 Kekulization
- [ ] 1.17 Sanitization pipeline and error types
- [ ] 1.18 Molecule edit API (add/remove atoms, bonds, add/remove Hs)
- [ ] 1.19 Connected components / fragment handling
### 1C Parsers and writers
- [ ] 1.20 SMILES recursive-descent zero-copy parser (organic subset, brackets, ring closures, branches, charges, isotopes, stereo, dot disconnects)
- [ ] 1.21 SMILES parser error reporting with positions
- [ ] 1.22 Canonical SMILES writer (canonical atom ranking)
- [ ] 1.23 SMILES fuzz tests (cargo-fuzz or proptest) and roundtrip tests
- [ ] 1.24 SMARTS parser (atomic primitives, logical operators, recursive SMARTS, bond primitives)
- [ ] 1.25 SDF/MOL V2000 reader/writer
- [ ] 1.26 SDF/MOL V3000 reader/writer
- [ ] 1.27 MOL2 reader/writer
- [ ] 1.28 Streaming multi-record readers (large libraries, memory-mapped input if license-compatible)
### 1D Fingerprints and search
- [ ] 1.29 Bit-vector fingerprint type (sparse and dense)
- [ ] 1.30 Morgan/ECFP (radius, count and bit variants, chirality option, invariants)
- [ ] 1.31 MACCS keys (166)
- [ ] 1.32 Topological torsion fingerprint
- [ ] 1.33 Tanimoto (and Dice, Cosine) via tpt-simd
- [ ] 1.34 VF2 substructure matcher on petgraph (atom/bond predicate hooks)
- [ ] 1.35 SMARTS-to-matcher compilation; uniquify/max-matches options
- [ ] 1.36 Rayon parallel search over compound libraries
- [ ] 1.37 Parallel batch parse + fingerprint pipeline
- [ ] 1.38 Golden tests vs RDKit: canonical SMILES, aromaticity, ring counts, ECFP, MACCS, substructure hits
- [ ] 1.39 Benchmarks: parse + fingerprint 1M SMILES vs RDKit
- [ ] 1.40 Minimal `tptmol search` prototype (parse + fingerprint + substructure)
- [ ] **Milestone 1:** `tptmol search` parses 1M SMILES and computes fingerprints faster than RDKit (benchmark published in repo)

## Phase 2: Physics & Minimization (spec months 4-6)
### 2A System construction
- [ ] 2.1 Topology/System model (atoms, bonds, angles, torsions, impropers, exclusions, 1-4 pairs)
- [ ] 2.2 Coordinates, box, PBC types (orthorhombic then triclinic)
- [ ] 2.3 Periodic boundary conditions and minimum-image
- [ ] 2.4 Distance-geometry 3D embedding from molecular graph (bounds matrix, triangle smoothing, embed, chirality fix)
- [ ] 2.5 ETKDG-style torsion/knowledge refinement (optional heuristic) and conformer generation
- [ ] 2.6 PDB reader/writer (protein + ligand, HETATM, altlocs, CONECT)
- [ ] 2.7 Residue templates and protein topology builder
- [ ] 2.8 Hydrogen placement for proteins and ligands
- [ ] 2.9 Solvation box builder (water placement) and ion addition
### 2B Force fields
- [ ] 2.10 Force-field parameter file format (own TOML/JSON; ensure parameter data licenses are permissive and documented)
- [ ] 2.11 Parameter data provenance audit (AMBER, GAFF, CHARMM, OPLS parameter licensing/redistribution terms)
- [ ] 2.12 Bond stretch, angle bend energies + gradients
- [ ] 2.13 Proper and improper torsions (periodic) + gradients
- [ ] 2.14 Lennard-Jones with mixing rules, 1-4 scaling
- [ ] 2.15 Coulomb electrostatics, 1-4 scaling
- [ ] 2.16 Nonbonded cutoffs/switching and long-range correction
- [ ] 2.17 PME (Ewald) electrostatics (or reaction-field fallback first)
- [ ] 2.18 AMBER force field (ff14SB/ff19SB-style protein, TIP3P water)
- [ ] 2.19 GAFF atom typing and ligand parameterization
- [ ] 2.20 Partial charge assignment (Gasteiger; document AM1-BCC out of scope or alternative)
- [ ] 2.21 CHARMM force field (CHARMM36 protein, CGenFF-style ligand typing)
- [ ] 2.22 OPLS force field (OPLS-AA)
- [ ] 2.23 Energy/force validation vs OpenMM on reference systems (per term and total)
### 2C Minimization and integration
- [ ] 2.24 Numerical-gradient checks for every energy term
- [ ] 2.25 L-BFGS minimizer via tpt-math
- [ ] 2.26 Conjugate gradient minimizer via tpt-math
- [ ] 2.27 Convergence criteria and steric-clash detection
- [ ] 2.28 Velocity Verlet integrator
- [ ] 2.29 Langevin thermostat (rand, seeded RNG reproducibility)
- [ ] 2.30 Nose-Hoover thermostat
- [ ] 2.31 SHAKE/RATTLE constraints (bonds to H, rigid water SETTLE)
- [ ] 2.32 Barostat (Monte Carlo and/or Berendsen/Parrinello-Rahman; document choice) for NPT
- [ ] 2.33 Maxwell-Boltzmann velocity initialization, COM motion removal
- [ ] 2.34 Trajectory output (DCD/XTC-like or simple binary + PDB frames; avoid GPL libs) and checkpoint/restart
- [ ] 2.35 CPU nonbonded path with neighbor lists + rayon
- [ ] 2.36 Energy-conservation (NVE) and temperature-distribution tests
- [ ] 2.37 Analysis: RMSD (with Kabsch alignment), RMSF, energy reporters
- [ ] 2.38 Binding free-energy estimate (MM-GBSA/MM-PBSA-style; GB model implementation)
- [ ] 2.39 Minimal `tptmol minimize` (SMILES -> 3D -> relaxed coordinates)
- [ ] **Milestone 2:** `tptmol minimize` produces stable 3D coordinates from 2D SMILES with no steric clashes

## Phase 3: GPU Acceleration (spec months 7-9)
- [ ] 3.1 tpt-molecule-compute crate on wgpu (device selection, adapter fallback, error handling)
- [ ] 3.2 Buffer management and host/device transfer of coordinates, forces, parameters
- [ ] 3.3 WGSL kernel: Lennard-Jones pairwise forces
- [ ] 3.4 WGSL kernel: Coulomb pairwise forces
- [ ] 3.5 Precision strategy (f32 with compensation or mixed precision; WGSL lacks f64: document and validate)
- [ ] 3.6 Cell-list construction in compute shaders
- [ ] 3.7 Verlet list build, skin distance, rebuild trigger
- [ ] 3.8 GPU PME / reciprocal-space (FFT in WGSL) or reaction-field on GPU
- [ ] 3.9 GPU integration step (keep state on device to avoid per-step transfers)
- [ ] 3.10 PBC support in kernels
- [ ] 3.11 Energy/force parity tests: GPU vs CPU reference within tolerance
- [ ] 3.12 Long-run stability test (energy drift, NVE) on GPU
- [ ] 3.13 Backend tests on Vulkan, Metal, DX12; software fallback (CI headless adapter)
- [ ] 3.14 Profiling and optimization (workgroup size, memory access, occupancy)
- [ ] 3.15 wasm32/WebGPU build and browser smoke test
- [ ] 3.16 Benchmarks: ns/day at 10k atoms vs OpenMM on comparable hardware
- [ ] 3.17 Minimal `tptmol simulate` end-to-end (protein-ligand, AMBER, Langevin, GPU)
- [ ] **Milestone 3:** `tptmol simulate` runs a 10,000-atom system at nanoseconds-per-day on a mid-range GPU, no CUDA C++

## Phase 4: CLI, Python Bindings & Release (spec months 10-12)
### 4A CLI (tptmol)
- [ ] 4.1 clap command structure: search, simulate, minimize (+ fp, convert, embed as needed)
- [ ] 4.2 Config files, progress reporting, logging, exit codes
- [ ] 4.3 Input/output format auto-detection
- [ ] 4.4 Shell completions and man page
- [ ] 4.5 CLI integration tests
- [ ] 4.6 End-to-end virtual-screening pipeline test (spec section 7: ingest -> filter -> 3D -> GPU MD -> analysis -> export)
### 4B Python bindings
- [ ] 4.7 PyO3 bindings crate (MIT/Apache-2.0 check), abi3 wheels
- [ ] 4.8 Python API: Molecule, SMILES/SDF IO, fingerprints, search, minimize, simulate, analysis
- [ ] 4.9 NumPy interop (check numpy-binding crate licenses)
- [ ] 4.10 maturin build; wheels for Windows/Linux/macOS (x86_64 + aarch64)
- [ ] 4.11 Python tests, type stubs (.pyi), Jupyter example notebook
### 4C Release
- [ ] 4.12 Publish benchmarks vs RDKit and OpenMM with methodology and reproducible scripts
- [ ] 4.13 API docs (rustdoc, mdBook/user guide), examples per crate
- [ ] 4.14 Full cargo-deny/cargo-audit pass; third-party license notice file (THIRD_PARTY_LICENSES)
- [ ] 4.15 CHANGELOG, semver, version bump to 0.1.0
- [ ] 4.16 Dry-run `cargo publish` in dependency order (core -> graph -> dynamics -> compute -> cli)
- [ ] 4.17 Publish to crates.io
- [ ] 4.18 Publish wheels to PyPI
- [ ] 4.19 GitHub release, binaries, announcement
- [ ] **Milestone 4:** v0.1.0 on crates.io and PyPI with published benchmarks

## Phase 5: Post-Release & Backlog
- [ ] 5.1 Triage issues, community feedback loop
- [ ] 5.2 Further RDKit parity gaps (descriptors, reactions, 2D depiction, more fingerprints)
- [ ] 5.3 Advanced sampling and free energy (FEP/TI, replica exchange)
- [ ] 5.4 Additional force fields / polarizable models
- [ ] 5.5 Multi-GPU and performance tuning
- [ ] 5.6 Browser demo (WebAssembly + WebGPU)
- [ ] 5.7 Ongoing license audit on every dependency update
