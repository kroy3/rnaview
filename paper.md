---
title: 'RNAview: A Python package for RNA structure visualization and analysis'
tags:
  - Python
  - RNA
  - bioinformatics
  - secondary structure
  - visualization
  - computational biology
authors:
  - name: Kushal Raj Roy
    orcid: 0000-0002-4664-7462
    corresponding: true
    affiliation: 1
affiliations:
  - name: Department of Biology and Biochemistry, University of Houston, Houston, TX, USA-77204
    index: 1
date: 14 December 2025
bibliography: paper.bib
---

# Summary

RNAview is a Python package for visualizing, analyzing, and exploring RNA secondary and tertiary structures. The package provides a unified programmatic interface for loading structures from multiple file formats, predicting secondary structures using established thermodynamic and machine learning-based methods, visualizing structures through diverse layout algorithms, and quantitatively comparing predicted structures against reference annotations. RNAview integrates with widely used prediction tools including ViennaRNA [@Lorenz2011], LinearFold [@Huang2019], and CONTRAfold [@Do2006], while providing built-in benchmark datasets for method validation. The software architecture (\autoref{fig:architecture}) enables seamless workflows from sequence input through structure prediction, visualization, and quantitative analysis. The package is available on GitHub (https://github.com/kroy3/rnaview) and PyPI (https://pypi.org/project/rnaview/).

# Statement of need

RNA secondary structure prediction and visualization are fundamental tasks in computational RNA biology, with applications spanning gene regulation, drug design, and understanding non-coding RNA function [@Mathews2019]. Researchers routinely need to visualize predicted or experimentally determined structures, compare alternative folding predictions, and assess prediction accuracy against known structures. However, the current landscape of RNA structure tools presents several challenges.

Existing visualization tools such as VARNA [@Darty2009], forna [@Kerpedjiev2015], and RNAcanvas [@Johnson2023] provide interactive drawing capabilities but lack programmatic interfaces suitable for high-throughput analysis pipelines. Structure prediction tools like ViennaRNA [@Lorenz2011] and LinearFold [@Huang2019] return results in varied formats, requiring custom parsing code. Analysis packages such as RNAstructure [@Reuter2010] lack native Python bindings, limiting accessibility to Python-centric workflows increasingly common in bioinformatics.

Recent deep learning advances have introduced prediction methods including SPOT-RNA [@Singh2019], MXfold2 [@Sato2021], and UFold [@Fu2022], creating additional complexity for researchers comparing multiple approaches. Benchmark datasets such as ArchiveII [@Sloma2016] and bpRNA [@Danaee2018] exist independently, requiring users to locate and parse each resource separately. Furthermore, the growing field of epitranscriptomics demands tools representing RNA modifications alongside secondary structure—a capability absent from most existing software.

RNAview addresses these challenges by consolidating structure representation, prediction, visualization, and analysis into a single Python package. The modular architecture (\autoref{fig:architecture}) comprises six layers: (1) file I/O supporting CT [@Zuker1981], BPSEQ [@Cannone2002], dot-bracket [@Hofacker1994], PDB, mmCIF, Stockholm, and FASTA formats; (2) a core data model with `RNAStructure` and `RNASequence` classes supporting over 70 RNA modifications; (3) a predictor integration layer providing unified access to ViennaRNA, LinearFold, CONTRAfold, and a built-in Nussinov algorithm [@Nussinov1978] fallback; (4) visualization supporting multiple 2D layouts (radiate, circular, NAView), arc diagrams, and 3D views with six colorblind-friendly palettes (\autoref{fig:visualizations}); (5) analysis metrics including sensitivity, positive predictive value, F1 score, and Matthews correlation coefficient [@Matthews1975; @Chicco2020]; and (6) curated benchmark datasets of tRNA and small RNA structures derived from Rfam [@Kalvari2021].

The package targets researchers developing prediction algorithms, analyzing large-scale RNA datasets, or integrating structure visualization into computational pipelines. By providing a consistent API across diverse formats, methods, and visualization styles, RNAview reduces technical overhead and enables reproducible research workflows.

![RNAview software architecture showing the modular design with six functional layers: file I/O parsers (blue), core data model and analysis components (green), visualization and export modules (red), and external prediction tool integrations (yellow). Arrows indicate data flow between components. The architecture enables flexible workflows from sequence input through structure prediction, analysis, and visualization output.\label{fig:architecture}](figure1_architecture.png)

![Example visualizations of yeast tRNA-Phe (76 nucleotides) generated by RNAview. (A) Arc diagram displaying base pairs as semicircular arcs above a linear sequence representation, effective for visualizing long-range interactions. (B) Radiate layout arranging nucleotides in a tree-like structure emanating from the 5' end. (C) Circular layout with RNA modifications highlighted, demonstrating the package's epitranscriptomic visualization capabilities. Nucleotides are colored by base type using a colorblind-friendly palette: adenine (orange), uracil (blue), guanine (green), cytosine (pink). Modified positions show m1G, m5C, and pseudouridine (Ψ) annotations.\label{fig:visualizations}](figure2_visualizations.png)

# Software Design

RNAview implements a layered architecture that separates concerns across data representation, external tool integration, visualization, and analysis. The design prioritizes extensibility and maintainability while providing a consistent interface for diverse RNA structure operations.

The core data model centers on two primary classes: `RNASequence` represents primary structure with support for modified nucleotides, while `RNAStructure` encodes secondary structure as base-pairing information alongside sequence data. Both classes implement validation logic ensuring structural consistency, such as verifying base pair complementarity and detecting pseudoknots. The modification support encompasses over 70 chemical variants catalogued in MODOMICS, enabling epitranscriptomic analysis workflows that have become increasingly important in RNA research.

File I/O operations employ format-specific parser classes that inherit from abstract base classes defining standard read and write interfaces. This polymorphic design allows users to load structures using a unified API regardless of source format, while individual parsers handle format-specific idiosyncrasies. For instance, the PDB parser extracts backbone coordinates and infers base pairing from spatial proximity, whereas the CT parser directly reads connectivity information from structured text files.

External prediction tool integration follows an adapter pattern where each predictor implements a common interface wrapping command-line execution, output parsing, and error handling. The design accommodates both local installations and optional dependencies, gracefully degrading to the built-in Nussinov implementation when external tools are unavailable. This approach balances power-user access to state-of-the-art methods with out-of-the-box functionality for users without extensive setup requirements.

Visualization components separate layout algorithms from rendering backends. Layout engines compute nucleotide coordinates using algorithms adapted from graph drawing literature, including the NAView algorithm for planar embeddings and force-directed approaches for circular layouts. Rendering backends then translate these coordinates into publication-quality graphics through matplotlib or interactive web-based displays. This separation enables reuse of layout computations across different output formats and facilitates future extensions to alternative rendering libraries.

The analysis module implements standard RNA structure comparison metrics as standalone functions that operate on `RNAStructure` objects. Metrics include per-base-pair sensitivity and specificity as well as aggregate scores accounting for class imbalance in RNA structure datasets. The functional design allows composition of custom analysis pipelines and integration with external validation frameworks.

# Research Impact Statement

RNAview addresses critical needs in computational RNA biology by providing researchers with an accessible, unified platform for structure analysis that previously required orchestrating multiple disparate tools. The package has immediate impact across several research domains.

For algorithm developers, RNAview streamlines the evaluation pipeline when creating new RNA folding prediction methods. Researchers can benchmark novel algorithms against established baselines using the included curated datasets, generate comparative visualizations, and compute standardized performance metrics—all within a consistent Python environment. This reduces the technical barrier to method development and promotes rigorous, reproducible comparisons essential for advancing the field.

In high-throughput transcriptomics studies, investigators analyzing thousands of non-coding RNA sequences need programmatic tools for structure prediction and visualization at scale. RNAview's batch processing capabilities and integration with pandas data structures enable incorporation of RNA structure predictions into large-scale genomics pipelines, supporting studies of RNA regulation, evolution, and functional annotation.

The epitranscriptomics visualization features support emerging research into RNA modifications and their structural consequences. By enabling joint representation of sequence, structure, and chemical modifications, RNAview facilitates hypothesis generation about modification-structure relationships and assists in designing experiments to test functional impacts of specific modifications.

Educational applications benefit from the package's clear API and extensive documentation. The included example workflows and Jupyter notebook tutorials lower the entry barrier for students and researchers new to computational RNA biology, supporting training of the next generation of RNA bioinformaticians.

# AI usage disclosure

AI-assisted tools were not used in the preparation of this manuscript.

# Acknowledgements

The author thanks the RNA bioinformatics community for developing and maintaining the foundational tools and databases that RNAview builds upon, including ViennaRNA, Rfam, and the benchmark datasets used for validation.

# References
