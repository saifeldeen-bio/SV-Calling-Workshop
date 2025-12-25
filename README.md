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

### 1.1 Enable Windows Features and WSL
In the search box, search for `Turn WIndows features on or off`

<img width="1071" height="1044" alt="image" src="https://github.com/user-attachments/assets/faa2a8a0-a58e-4b7a-953c-7be2df3fd9df" />

In the pop-up menu, enable the following:
   * `Virtual Machine Platform`
   * `Windows Subsystem For Linux`

<img width="520" height="464" alt="image" src="https://github.com/user-attachments/assets/6df25474-cb36-41f8-bf4e-ca171c63dd84" />

Click OK, and restart when he asks to restart your PC. After restarting, type in the search box again `command prompt` and open it.
In the command prompt terminal, type `wsl --status`, you will see that WSL has been installed successfully

<img width="1483" height="762" alt="image" src="https://github.com/user-attachments/assets/8e799e1f-b7fc-4740-b19f-6dbad08ea0e9" />

Now you have to update WSL to get the latest version before we install Ubuntu. 

In the **Command Prompt Terminal** run:

```powershell
wsl --update
```

<img width="1483" height="762" alt="image" src="https://github.com/user-attachments/assets/3d7bc981-4f5c-4bb1-990e-425c74d6d3a5" />

After installation, go to `Microsoft Store` and search for `Ubuntu` and Get it!!

<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/7f88bf3b-f2e9-4dbd-9a3a-524bd7c055e6" />

### 1.2 Launch Ubuntu

Once installed, open it from and set your username and password. Don't worry if you didn't see anything typed during the password step, it's a hidden process.

<img width="1483" height="762" alt="image" src="https://github.com/user-attachments/assets/fcbaf3f6-50cd-41bc-8e66-542cb21a1f47" />

#### Congratulations!!! Now you have Ubuntu Installed as subsystem on Windows!!
you can open Ubuntu Terminal from windows menu

---

## 2. 🧬 Install Miniconda & Configure Conda Channels

We use **Miniconda** to manage bioinformatics tools.

### 2.1 Download & install Miniconda (source: conda website)

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```
After installing, close and reopen your terminal application or refresh it by running the following command:
```bash
source ~/miniconda3/bin/activate
```
Then, initialize conda on all available shells by running the following command:

```bash
conda init --all
```
### 2.2 Configure Channels (including BioConda)

```bash
conda config --add channels defaults
conda config --add channels conda-forge
conda config --add channels bioconda
conda config --set channel_priority strict
```

## 3. 🧰 Install Key Tools
Now install essential tools for SV calling workflow. Create a new environment by runing 

```bash
conda create -n sv_env -y
conda activate sv_env

```

Accept all terms and conditions

### 🧪 3.1 FastQC (Quality Control)

```bash
conda install fastqc -y
fastqc --version
```
👉 FastQC lets you assess read quality. 

---

### 📊 3.2 MultiQC (aggregate report)

```bash
conda install multiqc -y
multiqc --version
```

👉 MultiQC summarizes FastQC outputs across samples.

---

### 🧬 3.3 BWA (Alignment)

```bash
conda install bwa -y
bwa
```
---
### 🔍 3.4 SRA Toolkit (Download public FASTQ)

```bash
conda install samtoools -y
```
---


### 🧠 3.5 Manta (Structural Variant Calling)

Manta requires additional setup, it's recommended to install it in a separate enironment

```bash
conda deactivate
conda create -n manta_env -y
conda activate manta_env
conda install manta -y
```
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

