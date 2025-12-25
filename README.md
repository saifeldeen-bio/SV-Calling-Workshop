# Structural Variant Calling in Practic Workshop

## 🛠️ Table of Contents

1. **Install Ubuntu on Windows (WSL)**
2. **Install Miniconda & Configure Bioinformatics Environment**
3. **Install Key Tools**
   * `FastQC`
   * `MultiQC`
   * `BWA`
   * `Samtools`
   * `DeepVariant`
   * `IGV`
4. **Download Datasets**
5. **SV Workflow**
---

## 1. 🐧 Install Ubuntu on Windows (WSL)

Follow these steps to enable WSL and install Ubuntu:

### 1.1 Enable Windows Features
In the search box, search for `Turn WIndows features on or off`

<img width="1071" height="1044" alt="image" src="https://github.com/user-attachments/assets/faa2a8a0-a58e-4b7a-953c-7be2df3fd9df" />

In the pop-up menu, enable the following:
   * `Virtual Machine Platform`
   * `Windows Subsystem For Linux`

<img width="520" height="464" alt="image" src="https://github.com/user-attachments/assets/6df25474-cb36-41f8-bf4e-ca171c63dd84" />

Click OK, and restart when he asks to restart your PC

Open **PowerShell as Administrator** and run:

```powershell
wsl --install
```

This installs WSL2 and defaults to Ubuntu. Reboot if prompted.

### 1.2 Launch Ubuntu

After installation:

* Open **Ubuntu** from the Start Menu.
* Set your username/password when prompted.

---

## 2. 🧬 Install Miniconda & Configure Conda Channels

We use **Miniconda** to manage bioinformatics tools.

### 2.1 Download & install Miniconda

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
# Follow prompts, then restart the shell
```

### 2.2 Configure Channels (including BioConda)

```bash
conda config --add channels defaults
conda config --add channels conda-forge
conda config --add channels bioconda
conda config --set channel_priority strict
```

---

## 3. 🧰 Install Key Tools

Now install essential tools for quality control, mapping, CNV calling, and visualization.

### 🧪 3.1 FastQC (Quality Control)

```bash
conda create -n qc_env fastqc -y
conda activate qc_env
fastqc --version
```

👉 FastQC lets you assess read quality. ([YouTube][1])

---

### 📊 3.2 MultiQC (aggregate report)

```bash
conda install multiqc -y
multiqc --version
```

👉 MultiQC summarizes FastQC outputs across samples. ([University Wiki Service][2])

---

### 🧬 3.3 BWA (Alignment)

```bash
conda install bwa -y
bwa
```

---

### 🧠 3.4 DeepVariant (Variant Calling)

DeepVariant requires additional setup with TensorFlow and a reference genome.

```bash
conda install -c conda-forge -c bioconda deepvariant -y
```

⚠️ You will need a human reference (e.g., GRCh38) and a GPU for faster runs (optional).

---

### 🔍 3.5 SRA Toolkit (Download public FASTQ)

```bash
conda install sra-tools -y
```

Use `fasterq-dump SRRXXXXX` to get FASTQ from SRA. ([NCBI][3])

---

### 🧬 IGV (Genome Visualization — GUI)

IGV is a **graphical tool**. It’s easiest to install from their website:

➡️ [https://software.broadinstitute.org/software/igv/](https://software.broadinstitute.org/software/igv/)
Download the latest Linux version (extract and run).

---

## 4. 📥 Download Sample FASTQ Datasets

To practice CNV and NGS analysis, you can use **public data**:

### 🔹 1) SRA / ENA — Human sequencing data

The **Sequence Read Archive (SRA)** and **European Nucleotide Archive (ENA)** host real sequencing runs in FASTQ format. ([Wikipedia][4])

Example collections include:

* **1000 Genomes Project** — Whole genomes (healthy individuals). ([Wikipedia][5])
* Custom SRA studies with CNV phenotypes: search for “cancer exome CNV” or similar in SRA.

#### Download FASTQ

Inside WSL:

```bash
# Example Run accession
fasterq-dump SRR12345678
```

You should get paired files like:

```
SRR12345678_1.fastq
SRR12345678_2.fastq
```

---

### 🔹 2) Small test datasets

For initial testing, small fastq samples are available on GitHub:

➡️ **hartwigmedical/testdata** (FASTQ and small datasets) ([GitHub][6])

```bash
git clone https://github.com/hartwigmedical/testdata.git
```

These contain example FASTQ files (not necessarily disease vs normal with CNVs but great for tool testing).

---

## 5. 🧠 Example Workflow

### 5.1 Quality Control

```bash
fastqc *.fastq
multiqc .
```

### 5.2 Alignment with BWA

```bash
bwa index ref.fa
bwa mem ref.fa sample_1.fastq sample_2.fastq > sample.sam
```

### 5.3 Convert SAM → BAM and sort

```bash
samtools view -b sample.sam | samtools sort -o sample.sorted.bam
samtools index sample.sorted.bam
```

### 5.4 Variant Calling (DeepVariant)

```bash
deepvariant --model_type=WGS --ref=ref.fa \
  --reads=sample.sorted.bam \
  --output_vcf=sample.vcf.gz
```

---

## 6. 🔬 Visualize Results

### IGV

* Load your **reference genome**
* Load **BAM** and **VCF**
* Inspect regions with **suspected CNVs** (deletions/duplications)

---

## 7. 💡 Tips & Next Steps

✅ Use **CNV callers** such as **GATK CNV, CNVkit, XHMM**, or others after alignment (not covered here).
✅ Get **paired tumor-normal** datasets from SRA to benchmark CNV detection.
✅ For exome CNV: consider **target regions BED files** and tools optimized for exomes.

---

## 8. 📚 References & Resources

* FastQC basic guide and install details. ([YouTube][1])
* MultiQC overview for summarizing reports. ([University Wiki Service][2])
* SRA Toolkit instructions for raw sequence download. ([NCBI][3])
* Public sequence repositories (SRA/ENA). ([Wikipedia][4])

---

Let me know if you want a **script to automate these steps** (e.g., a Bash installer) or a **workflow with specific CNV callers** included!

[1]: https://www.youtube.com/watch?v=5nth7o_-f0Q&utm_source=chatgpt.com "Fastqc Tutorial | Linux Install and Usage (Commandline & GUI)"
[2]: https://cloud.wikis.utexas.edu/wiki/display/bioiteam/MultiQC%2B-%2BfastQC%2Bsummary%2Btool%2B--%2BGVA2022?utm_source=chatgpt.com "Installing multiqc - University Wiki Service"
[3]: https://www.ncbi.nlm.nih.gov/sra/docs/sradownload/?utm_source=chatgpt.com "Download SRA sequences from Entrez search results - NCBI - NIH"
[4]: https://en.wikipedia.org/wiki/Sequence_Read_Archive?utm_source=chatgpt.com "Sequence Read Archive"
[5]: https://en.wikipedia.org/wiki/1000_Genomes_Project?utm_source=chatgpt.com "1000 Genomes Project"
[6]: https://github.com/hartwigmedical/testdata?utm_source=chatgpt.com "hartwigmedical/testdata: Small datasets for testing purpose ... - GitHub"

