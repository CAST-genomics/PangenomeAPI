# User Guide

## Hosted datasets

The Pangenome Browser currently hosts datasets from the HPRC version 1 and version 2 releases:

**Version 1 - HPRC release 1**

- 47 phased, diploid genome assemblies (94 haplotypes, CHM13, GRCh38)
- Pangenome Browser features not available for version 1 graphs: PCLAI and PCLAI chart

**Version 2 - HPRC release 2**

- 232 phased, diploid genome assemblies (464 haplotypes, CHM13, GRCh38)
- All features in the Pangenome Browser are available for version 2 graphs

## Graph Types and Layout

The Pangenome Browser currently supports Minigraph in Bandage layout, and Minigraph-Cactus in Sequence Tube Map layout:

### Minigraph graphs (rendered in Bandage)

Minigraph graph was built by starting from the reference assembly, and iteratively and progressively adding in additional assemblies. Minigraph graph represents *structural variants (SVs) larger than 50 bp*. It is the default graph type used by the UCSD Pangenome Browser. The visualization of the Minigraph graph is rendered using the same layout algorithm as implemented in [Bandage](https://rrwick.github.io/Bandage/).

- Minigraph HPRC Resources
  - [Minigraph Release 1](https://github.com/human-pangenomics/hpp_pangenome_resources#minigraph) - HPRC Release 1 reference Minigraph
  - [Minigraph Release 2](https://s3-us-west-2.amazonaws.com/human-pangenomics/index.html?prefix=pangenomes/scratch/2025_09_08_minigraph_cactus/) - HPRC Release 2 reference Minigraph
- [Minigraph GitHub](https://github.com/lh3/minigraph) - GitHub page of the minigraph tool
- [Minigraph Paper](https://link.springer.com/article/10.1186/s13059-020-02168-z) - "The design and construction of reference pangenome graphs with minigraph"

### Minigraph-Cactus graphs (rendered in Sequence Tube Map)

Minigraph-Cactus graph extends the Minigraph pangenome with a base-level alignment of the homology relationships between the assemblies using the Cactus genome aligner. It retains the structure of the Minigraph pangenome. In the Pangenome Browser, Minigraph-Cactus graph visualization is only available by clicking a Minigraph node. The visualization of the Minigraph-Cactus graph is rendered in [Sequence Tube Map](https://github.com/vgteam/sequencetubemap).

- Minigraph-Cactus
  - [Minigraph-Cactus Release 1](https://github.com/human-pangenomics/hpp_pangenome_resources#minigraph-cactus) - HPRC Release 1 reference Minigraph-Cactus
  - [Minigraph-Cactus Release 2](https://s3-us-west-2.amazonaws.com/human-pangenomics/index.html?prefix=pangenomes/scratch/2025_09_08_minigraph_cactus/) - HPRC Release 2 reference Minigraph-Cactus
- [Minigraph-Cactus Pipeline](https://github.com/ComparativeGenomicsToolkit/cactus/blob/master/doc/pangenome.md) - The Minigraph-Cactus Pangenome Pipeline
- [Minigraph-Cactus Paper](https://www.nature.com/articles/s41587-023-01793-w) - "Pangenome graph construction from genome alignments with Minigraph-Cactus"

## Display Views

We currently support visualizations of three different types of genome representations in the browser:

### Assembly view

Selecting the assembly view will open a box with available assemblies. When an assembly is selected, every node it traverses is colored dark red. Users can trace that assembly's path through the graph and visually compare where different assemblies' paths converge or diverge. Hovering over any node displays its node ID and node length.

### Population view

Selecting the population view will open a box with available populations. When a population is selected, the graph is rendered as a heatmap: each node is colored according to the percentage of assemblies within that population that traverse it:

- solid red indicates 100% of the population's assemblies traverse the node
- solid blue indicates 0% of the population's assemblies traverse the node
- intermediate percentages fall on a gradient between the two

Hovering over a node displays a full breakdown across all populations: for each population, the number of assemblies traversing the node and the percentage of that population's assemblies traversing it.

### PCLAI (Point Cloud Local Ancestry Inference) view

PCLAI (Point Cloud Local Ancestry Inference) infers continuous population genetic structure along the genome, rather than assigning each genomic window to a discrete ancestry label. The Pangenome Browser incorporates PCLAI annotations computed for HPRC Release 2.

#### Mapping PCLAI windows onto Minigraph graph nodes

PCLAI predicts local ancestries on fixed genomic windows, while the Pangenome Browser's Minigraph graph is organized by node, so each node's color must be derived from the window(s) it overlaps. When a node's color must be interpolated between two windows, the Pangenome Browser applies an inpainting method suggested by the PCLAI team: the Euclidean distance between the two windows' coordinates in PCA space is computed; if it falls below a discontinuity threshold (x = 0.105), the node is colored with the average of the two windows' colors, otherwise it is left unpainted.

| Node's relation to PCLAI windows | Coloring logic |
| --- | --- |
| Falls entirely within one PCLAI window | Colored with that window's color |
| Spans exactly 2 PCLAI windows | Inpainting rule applied between the two windows |
| Spans 3 or more PCLAI windows | Left unpainted |
| Falls outside any PCLAI window | Inpainting rule applied between the two adjacent windows |

#### Reference coordinates and per-assembly filtering

PCLAI annotations currently loaded in the Pangenome Browser are computed on GRCh38 coordinates. Thus, some nodes carrying PCLAI data of the selected assembly are absent from this assembly's path. PGB filters these out, displaying PCLAI color only for nodes present in the currently selected assembly.

When an assembly is selected:

| Color | Meaning |
| --- | --- |
| PCLAI color (PCA-derived) | Node exists in the selected assembly and has a predicted PCLAI color |
| Dark grey | Node exists in the selected assembly, but has no predicted color (left unpainted per the node-PCLAI mapping rules) |
| Light grey | Node does not exist in the selected assembly |

#### Linking the graph to the PCLAI chart

- When an assembly is selected, the PCA points for all colored (non-grey) nodes currently in view are highlighted in the PCLAI chart.
- Hovering over a node highlights only that node's PCA point.
- When no assembly is selected, hovering over a node highlights the PCA points for that node across every assembly traversing it.

Source PCLAI dataset we used:

- [HPRC Release 2 PCLAI Annotation](https://github.com/human-pangenomics/hprc_intermediate_assembly/tree/main/data_tables/annotation/pclai)
  - [pclai_v1.1_grch38_coord_local_hprc_r2.index.csv](https://github.com/human-pangenomics/hprc_intermediate_assembly/blob/main/data_tables/annotation/pclai/pclai_v1.1_asm_coord_local_hprc_r2.index.csv) used for the browser
  - [pclai_v1.1_asm_coord_local_hprc_r2.index.csv](https://github.com/human-pangenomics/hprc_intermediate_assembly/blob/main/data_tables/annotation/pclai/pclai_v1.1_asm_coord_local_hprc_r2.index.csv) available in PangenomeAPI
  - [pclai_v1.1_chm13_coord_local_hprc_r2.index.csv](https://github.com/human-pangenomics/hprc_intermediate_assembly/blob/main/data_tables/annotation/pclai/pclai_v1.1_asm_coord_local_hprc_r2.index.csv) not available in the browser or the PangenomeAPI

Learn more about PCLAI:

- [HPRC PCLAI](https://github.com/AI-sandbox/hprc-pclai) - Documentation for PCLAI performed on HPRC Release 2
- [PCLAI](https://github.com/AI-sandbox/pclai) - Point Cloud Local Ancestry Inference GitHub Code
- [PCLAI preprint](https://www.biorxiv.org/content/10.64898/2026.03.23.713813v1) - "Point cloud local ancestry inference (PCLAI): continuous coordinate-based ancestry along the genome"

## Getting Started

### Browser layout and navigation

The Pangenome Browser interface is organized into three regions: a top toolbar, a left-hand navigation panel, and a central graph canvas where the pangenome graph itself is rendered.

#### Top toolbar (left to right)

- **Version toggle** — switch between data releases
- **Search box** — enter a gene name or genomic coordinates to load a region
- **[Print icon]** — export the current annotation track, pangenome graph, or PCLAI chart
- **PCLAI chart** — opens a PCA (principal component analysis) chart. It is accessible at any time, but is most relevant when viewing the PCLAI display mode
- **Release information** — displays the current browser release version

#### Left navigation panel

- Three display-view tabs: **Assembly**, **Population**, **PCLAI**
- Search box within the display views can help users browse specific assemblies of interest

### Searching for a gene or genomic region

1. Search by gene name (e.g., *BRCA1*, *MACROD2*, *LPA*)
2. Search by genomic coordinate `chr:start-end` (e.g., `chr1:25240000-25460000`)
3. Submit the query (press **GO**) to load the corresponding pangenome graph into the central canvas.
4. **Note:** for Minigraph graphs, the region actually rendered may extend beyond the searched region on both sides — the returned region is not guaranteed to match the query exactly.
5. Once a Minigraph graph is rendered, clicking any node opens the corresponding Minigraph-Cactus visualization for that node's region, letting users move from a graph-wide overview (Minigraph) to a base-level, path-resolved view (Minigraph-Cactus) of any node region of interest.

### Interacting with the Graph

#### Panning

- Pan: click-and-drag on the canvas

#### Graph-and-track interactions

Moving the mouse along the graph displays a corresponding cursor on the gene annotation track, and moving the mouse along the annotation track displays a corresponding cursor on the graph — letting users directly cross-reference a position in the graph with its location in the linear annotation, and vice versa.

#### Hovering over a node

- **Inside Population display view** – when a specific population is selected, hovering over a node displays a list of populations, number of assemblies traversing the node, and the percentage of assemblies (within that population) traversing it
- **Inside PCLAI display view** – hovering over a node displays Node ID, node length, and a confidence score for the node's PCLAI; also highlights the node's corresponding PCLAI coordinates on the PCA plot
- **Inside other views** – hovering over a node displays node ID and node length

#### Right-clicking a node

Opens a context menu with two options:

- **Copy Sequence** — copies the node's complete nucleotide sequence
- **Copy Assemblies, Haplotypes, Sequence IDs, and BP Ranges** — copies a list of assemblies traversing the node, with each assembly's coordinates, in `sample#haplotype#contig<TAB>start-end` format (PanSN-spec naming)

Example output:

```text
HG00408#1#JBHDVK010000002.1	26,066,275-26,118,080
HG00597#1#CM085766.1	25,696,176-25,748,006
HG01192#1#JBHDVI010000055.1	24,489,604-24,541,409
```

## Data & the PangenomeAPI

The PangenomeAPI is the backend that powers the Pangenome Browser.

Once a region has been entered and returned, the Browser fetches a request to a PangenomeAPI endpoint, passing the genomic region, the requested version, and the requested graph type. The PangenomeAPI then:

1. subsets the reference graph based on the requested region
2. annotates each node with metadata (assembly list, PCLAI data, and assembly coordinates)
3. runs the layout algorithm to compute the graph's coordinates

The result is then returned to the Browser, which renders the graph from that data.

The PangenomeAPI can also be requested directly, independently of the Browser. This lets users work with the source data behind any Browser view for their own downstream analysis. For more information, see the [PangenomeAPI GitHub page](https://github.com/CAST-genomics/PangenomeAPI).

## Contact Us

Cici Bu: [xbu@ucsd.edu](mailto:xbu@ucsd.edu)