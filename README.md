pepdata
=======

An important aspect of computational immunology is modeling the properties of [peptides](http://en.wikipedia.org/wiki/Peptide) (short strings of amino acids). Peptides can arise as substrings [cut](http://en.wikipedia.org/wiki/Proteolysis) out of a larger protein, naturally occurring [small proteins](http://en.wikipedia.org/wiki/Alpha-Amanitin), or be [synthesized](micchm01.u.hpc.mssm.edu/dashboard/accounts/activate/e2b4804ac4d7e59dcff89a474d1971b8a36dff77/
) for therapeutic purposes. 
To make useful clinical and research predictions (i.e. "which peptides should go in this vaccine?") we need to partition the combinatorial space of peptides into classes such as [T-cell epitopes](http://en.wikipedia.org/wiki/Epitope) or [MHC](http://en.wikipedia.org/wiki/Major_histocompatibility_complex) ligands. One way to capture such distinctions is to collect large volumes of data about peptides and use that data to build statistical models of their immune properties. This library helps you build such models by providing simple Python/NumPy/Pandas interfaces to commonly used immunology and bioinformatics datasets. 

**Data Sources** 

- `iedb`: [Immune Epitope Database](http://www.iedb.org), large collection of epitope assay results for MHC binding as well as T-cell/B-cell responses
- `tcga`: Variant peptide substrings extracted from [TCGA](http://en.wikipedia.org/wiki/The_Cancer_Genome_Atlas) mutations across all cancer types
- `reference`: Peptide substrings from the [human reference protein sequence](ftp://ftp.ensembl.org/pub/release-75/fasta/homo_sapiens/pep/)
- `imma2`: IMMA2 epitope immunogenic vs. non-immunogenic data set used by Tung et al. for evaluating the [POPISK](http://www.biomedcentral.com/1471-2105/12/446) immunogenicity predictor 
- `calis`: Two datasets used in Calis et al.'s [Properties of MHC Class I Presented Peptides That Enhance Immunogenicity](http://www.ploscompbiol.org/article/info%3Adoi%2F10.1371%2Fjournal.pcbi.1003266#pcbi.1003266.s005) 
- `hpv`: [Human Papillomavirus T cell Antigen Database](http://cvc.dfci.harvard.edu/cvccgi/hpv/)
- `toxin`: Toxic protein sequences from [Animal Toxin Databse](http://protchem.hunnu.edu.cn/toxin/)
- `danafarber`: [Dana Farber Repository for Machine Learning in Immunology](http://bio.dfci.harvard.edu/DFRMLI/)
- `tantigen`: [Tumor T-cell Antigen Database](http://cvc.dfci.harvard.edu/tadb/)
- `hiv_frahm`: Reactions to HIV epitopes across different ethnicities (from [LANL HIV Databases](http://www.hiv.lanl.gov/content/immunology/hlatem/study1/index.html))
- `cri_tumor_antigens`: Tumor associated peptides from [Cancer Immunity](http://cancerimmunity.org/peptide/mutations/)
- `fritsch_neoepitopes`: Mutated and wildtype tumor epitopes from Fritsch et al. [HLA-binding properties of tumor neoepitopes in humans](http://cancerimmunolres.aacrjournals.org/content/early/2014/03/01/2326-6066.CIR-13-0227.abstract) 

Planned:

- `bcipep`: [B-cell epitopes](http://www.imtech.res.in/raghava/bcipep/data.html) 


**Dataset API**

When a dataset consists only of an unlabeled list of epitopes, then it only needs two functions:
- `load_wuzzle`: Returns set of amino acid strings 
- `load_wuzzle_ngrams`: Array whose rows are amino acids transformed into n-gram vector space. 

If the dataset contains additional data about the epitopes (such as HLA type u or source protein):
- `load_wuzzle`: Returns data frame with epitope strings and additional properties
- `load_wuzzle_set`: Set of epitope amino acid strings 
- `load_wuzzle_ngrams`: Array whose rows are amino acids transformed into n-gram vector space. 

If the dataset is labeled (contains positive and negative assay results), then the following functions should be available: 
- `load_wuzzle`: Load all available data from the "wuzzle" dataset (filtered by options such as `mhc_class`). 
- `load_wuzzle_values`: Group the dataset by epitope string and associate each epitope with the positive and negative counts, along with percentage of positive results (in a column called "value"). 
- `load_wuzzle_classes`: Split the epitopes into positive and negative classes, return a set of strings for each. 
- `load_wuzzle_ngrams`: Transform the amino acid string representation (or some reduced alphabet) into vectors of n-gram frequencies, return a sklearn-compatible `(samples, labels)` pair of arrays.   

**Amino Acid Properties** 

The `amino_acid` module contains a variety of physical/chemical properties for both single amino residues and interactions between pairs of residues. 

Single residue feature tables are parsed into `StringTransformer` objects, which can be treated as dictionaries or will vectorize a string when you call their method `transform_string`. 

Examples of single residue features:
- `hydropathy`
- `volume`
- `polarity`
- `pK_side_chain`
- `prct_exposed_residues`
- `hydrophilicity`
- `accessible_surface_area`
- `refractivity`
- `local_flexibility`
- `accessible_surface_area_folded`
- `alpha_helix_score` (Chou-Fasman)
- `beta_sheet_score` (Chou-Fasman)
- `turn_score` (Chou-Fasman)

Pairwise interaction tables are parsed into nested dictionaries, so that the interaction between amino acids `x` and `y` can be determined from `d[x][y]`. 

Pairwise interaction dictionaries:
- `strand_vs_coil` (and its transpose `coil_vs_strand`)
- `helix_vs_strand` (and its transpose `strand_vs_helix`)
- `helix_vs_coil` (and its transpose `coil_vs_helix`)
- `blosum30`
- `blosum50`
- `blosum62`

There is also a function to parse the coefficients of the [PMBEC similarity matrix](http://www.biomedcentral.com/1471-2105/10/394), though this currently lives in the separate `pmbec` module. 

