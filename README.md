# Mac Apple Silicon — Terminal Setup for Genovi

Circular genome map plotting on arm64 (M1 / M2 / M3 / M4) chips

![Apple Silicon](https://img.shields.io/badge/Apple%20Silicon-arm64-blue)
![Genovi Ready](https://img.shields.io/badge/Genovi-Ready-green)
![Rosetta 2](https://img.shields.io/badge/Rosetta%202-x86__64-yellow)
![Bioinformatics](https://img.shields.io/badge/Bioinformatics-Genome%20Plotting-purple)

---

# Background

## Architecture Note

Apple Silicon (M1–M4) uses **arm64** architecture.  
Some bioinformatics tools like **Genovi / Circos** were originally built for **x86_64** (Intel/AMD).

To bridge this compatibility gap, we use **Rosetta 2** as an emulation layer.

---

## What We Will Do

- Install Rosetta 2 for x86_64 emulation
- Add shell aliases to switch architectures
- Install Miniconda (x86_64 build)
- Install Genovi and Circos

---

# Preparation

Before starting:

1. Quit Terminal (`⌘ + Q`)
2. Open:

```text
Finder → Applications → Utilities
```

3. Right-click **Terminal** → **Get Info**
4. Ensure:

```text
"Open using Rosetta"
```

is **unchecked**

---

# Installation Steps

---

## Step 1 — Install Rosetta 2

Open Terminal and run:

```bash
softwareupdate --install-rosetta --agree-to-license
```

---

## Step 2 — Add Architecture Switching Aliases

### Step 2a — Open `.zshrc`

```bash
nano ~/.zshrc
```

---

### Step 2b — Add These Aliases

```bash
# Architecture switching aliases
alias arm="env /usr/bin/arch -arm64 /bin/zsh --login"
alias intel="env /usr/bin/arch -x86_64 /bin/zsh --login"
```

---

### Step 2c — Save and Reload

Save with:

```text
Ctrl + X → Y → Enter
```

Then reload:

```bash
source ~/.zshrc
```

---

### Test the Aliases

Switch to x86_64:

```bash
intel
```

Switch back to arm64:

```bash
arm
```

---

# Step 3 — Install Miniconda (x86_64)

## Step 3a — Switch to x86_64

```bash
intel
```

---

## Step 3b — Download Miniconda

```bash
curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
```

---

## Step 3c — Install to `~/miniconda-x86`

```bash
bash Miniconda3-latest-MacOSX-x86_64.sh -p ~/miniconda-x86
```

---

## Step 3d — Reload and Verify

```bash
source ~/.zshrc
```

Quit Terminal (`⌘Q`) and reopen.

Then verify:

```bash
conda info
```

---

# Step 4 — Install Genovi

## Step 4a — Switch to x86_64 and Confirm

```bash
intel
arch
```

Expected output:

```text
i386
```

---

## Step 4b — Create Genovi Environment

```bash
conda create -n genovi python=3.7 circos -c bioconda -c conda-forge
```

---

## Step 4c — Activate and Install Genovi

```bash
conda activate genovi
pip install genovi
```

---

# Create Circular Plot

After installation, generate a circular genome map using:

```bash
genovi -i NTD75_after.gbk \
       -s draft \
       -t 'Actinacidiphila sp. NTD75' \
       --title_position top \
       --italic_words 1 \
       -cs blossom \
       -te \
       --size
```

---

# Troubleshooting

---

## Error: Missing Perl Library

If:

```bash
circos --version
```

fails due to missing Perl libraries:

### Install `cpanminus`

```bash
conda install -n genovi -c conda-forge perl-app-cpanminus
```

---

### Install Required Perl Modules

```bash
cpanm Font::TTF::Font Config::General GD GD::Polyline \
      Math::VecStat Math::Bezier SVG Statistics::Basic \
      Readonly Set::IntSpan Text::Format
```

---

## Expected Versions

```bash
perl --version
```

Expected:

```text
v5.32
```

---

```bash
circos --version
```

Expected:

```text
circos | v 0.69-8 | 15 Jun 2019 | Perl 5.032001
```

---

# Permanent Perl Path Configuration

Add the following to:

```bash
~/.zshrc
```

```bash
# Force conda genovi perl
export PATH=/Users/YOUR_USERNAME/miniconda-x86/envs/genovi/bin:$PATH

export PERL5LIB=/Users/YOUR_USERNAME/miniconda-x86/envs/genovi/lib/perl5/5.32/site_perl:/Users/YOUR_USERNAME/miniconda-x86/envs/genovi/lib/perl5/5.32:$PERL5LIB
```

Reload:

```bash
source ~/.zshrc
```

---

# COG Functional Categories

| Code | Functional Category |
|---|---|
| D | Cell cycle control, division, chromosome partitioning |
| M | Cell wall/membrane/envelope biogenesis |
| N | Cell motility |
| O | Post-translational modification, protein turnover, chaperones |
| T | Signal transduction mechanism |
| U | Intracellular trafficking, secretion, and vesicular transport |
| V | Defense mechanism |
| W | Extracellular structures |
| Y | Nuclear structure |
| Z | Cytoskeleton |
| A | RNA processing and modification |
| B | Chromatin structure and dynamics |
| J | Translation, ribosomal structure, and biogenesis |
| K | Transcription |
| L | Replication, recombination, and repair |
| X | Mobilome: prophages, transposons |
| C | Energy production and conversion |
| E | Amino acid transport and metabolism |
| F | Nucleotide transport and metabolism |
| G | Carbohydrate transport and metabolism |
| H | Coenzyme transport and metabolism |
| I | Lipid transport and metabolism |
| P | Inorganic ion transport and metabolism |
| Q | Secondary metabolites biosynthesis, transport, and metabolism |
| R | General function prediction only |
| S | Function unknown |

---

# Notes

- Genovi works best inside the dedicated `genovi` conda environment.
- Always use the `intel` shell when running x86_64 bioinformatics tools.
- Apple Silicon native support for some dependencies is still incomplete, which is why Rosetta 2 is required.
