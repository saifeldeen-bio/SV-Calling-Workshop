# Structural Variant Calling in Practic Workshop

## 🛠️ Table of Contents

1. **Install Ubuntu on Windows (WSL)**
2. **Install Miniconda & Configure Bioinformatics Environment**
3. **Install Key Tools**
   * `FastQC`
   * `MultiQC`
   * `Trimmomatic`
   * `BWA`
   * `Samtools`
   * `Manta`
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

```

Accept all terms and conditions

### 🧪 3.1 FastQC & MultiQC (Quality Control)

```bash
conda create -n qc_env \
  -c conda-forge -c bioconda \
  python=3.10 fastqc multiqc \
  -y
```

---

### 📊 3.2 Trimmomatic

```bash
conda activate sv_env
conda install trimmomatic
```
---

### 🧬 3.3 BWA (Alignment)

```bash
conda install bwa -y
bwa
```
---
### 🔍 3.4 Samtools

```bash
conda create -n align \
  -c conda-forge -c bioconda \
  python=3.10 \
  samtools -y
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

```bash
conda deactivate
conda create -n igv_gui -c conda-forge -c bioconda igv openjdk=21 -y
conda activate igv_gui
```
---

## 4. 📥 Download Datasets

To easly practice SV analysis, we can use **small sample (e.g. microbiom sample)** to test and run our workflow faster, then we can explor a real human samples, from previous case studies, with IGV after getting the BAM files and VCFs to see the SVs

### 🔹 1) Fastq data

Inside your terminal create a diretory called `sv_analysis` and go inside it and create another directory called `raw_reads` to store your fastq files:

```bash
mkdir -p sv_analysis/raw_reads
cd sv_analysis/raw_reads
wget https://zenodo.org/record/3960260/files/004-2_1.fastq.gz
wget https://zenodo.org/record/3960260/files/004-2_2.fastq.gz
```

### 🔹 2) Reference Genome

```bash
cd ../
mkdir ref/
cd ref/
wget https://zenodo.org/record/3960260/files/MTB_ancestor_reference.fasta
```
---
