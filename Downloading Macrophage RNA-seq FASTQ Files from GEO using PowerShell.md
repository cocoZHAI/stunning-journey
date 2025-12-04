# A reproducible workflow using SRA Toolkit on Windows

## This guide walks through downloading raw FASTQ files (paired-end RNA-seq) from the GEO dataset GSE130011, which includes human macrophage activation states:

<img width="760" height="368" alt="image" src="https://github.com/user-attachments/assets/754c17c6-d0be-4aac-be30-98c689ced27e" />


#### The link to the paper: https://pmc.ncbi.nlm.nih.gov/articles/PMC10902381/?utm_source=chatgpt.com#CR76

## First: download the FASTQ files for each read

### Step 1: Install SRA tool kit
- Check out the github page: https://github.com/ncbi/sra-tools/wiki/01.-Downloading-SRA-Toolkit
- Make sure tools are added to PATH:

### Step 2: download metadata
- For instance: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE130011
- Go to `SRA Run Selector`
- Select the files you need and then download `metadata`
<img width="1831" height="635" alt="image" src="https://github.com/user-attachments/assets/ddb003de-d693-41ff-9a56-d55c7e6f5f2d" />

### Step 3: Create SRR_list.txt
- Load the CSV
```
$metadata = Import-Csv "C:\Users\pc\Desktop\RNAseq\SraRunTable.csv"
```
- Write the "Run" column into the txt file
```
$metadata."Run" | Out-File "C:\Users\pc\Desktop\RNAseq\SRR_list.txt" -Encoding ascii
```
### Step 4: Create the download_fastq.ps1

```
$SRR_list = "C:\Users\pc\Desktop\RNAseq\SRR_list.txt"
$OutDir = "C:\Users\pc\Desktop\RNAseq\data"

# Create the main output folder if needed
if (!(Test-Path $OutDir)) {
    New-Item -ItemType Directory -Path $OutDir | Out-Null
}

$SRRs = Get-Content $SRR_list

foreach ($SRR in $SRRs) {

    Write-Host "Processing $SRR ..."

    # Make subfolder: OutDir/SRRxxxxxxx
    $SRRdir = Join-Path $OutDir $SRR
    if (!(Test-Path $SRRdir)) {
        New-Item -ItemType Directory -Path $SRRdir | Out-Null
    }

    # Download SRA and convert to FASTQ (no compression)
    fasterq-dump $SRR --split-files --outdir $SRRdir

    Write-Host "$SRR completed."
}
```
### Step 5: Run the script
```
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\download_fastq.ps1
```
## Second: Alignment - tell the computer where each read come from the genome
- input: FASTQ (raw read)
- Output: BAM (reads aligned to the genome)

### Step 1: Go to https://www.gencodegenes.org/human/
- Download Basic gene annotation → CHR → GTF
  
Why this one?
It contains only the main chromosomes (no weird haplotypes or patches).
It’s clean and what most RNA-seq pipelines use.
“Basic” = removes low-confidence transcripts you don’t need.
This avoids alignment headaches and makes junction counting easier.

- Download Genome sequence, primary assembly (GRCh38) → PRI → FASTA
Why this one?
It matches the GTF chromosome names
It’s the standard genome used for most human RNA-seq
No alt haplotypes, no patch sequences — simpler, cleaner, faster alignment
Exactly what STAR expects for normal alignment

### Step 2: download ubuntu (STAR requires Linux system)
#### Download ubuntu manually
```
wsl --install -d Ubuntu

```
#### Create ubuntu shortcut on the desktop
- Right click -> "New" -> "shortcut"
- Enter ``` wsl.exe ``` into the location

#### Create a clean working directory inside Linux
```
mkdir -p ~/rnaseq/tools
mkdir -p ~/rnaseq/genome
mkdir -p ~/rnaseq/fastq
cd ~/rnaseq

```

#### Move STAR files from Windows into WSL
```
cp -r /mnt/c/Users/pc/Desktop/RNAseq/STAR-2.7.11b ~/rnaseq/tools/

```

#### Make STAR file executible and test the version
```
chmod +x bin/Linux_x86_64_static/STAR

```
```
bin/Linux_x86_64_static/STAR --version
```

#### Move your genome files into the genome folder
```
cp /mnt/c/Users/pc/Desktop/RNAseq/genome/GRCh38.primary_assembly.genome.fa ~/rnaseq/genome/
cp /mnt/c/Users/pc/Desktop/RNAseq/genome/gencode.v49.basic.annotation.gtf ~/rnaseq/genome/

```
#### Generate the STAR genome index
```
cd ~/rnaseq/genome                                                      
~/rnaseq/tools/STAR-2.7.11b/bin/Linux_x86_64_static/STAR \
  --runThreadN 8 \
  --runMode genomeGenerate \
  --genomeDir ./STARindex \
  --genomeFastaFiles GRCh38.primary_assembly.genome.fa \
  --sjdbGTFfile gencode.v49.basic.annotation.gtf \
  --sjdbOverhang 149

```
#### Align all your FASTQ samples
```
cd /mnt/c/Users/pc/Desktop/RNAseq/Macrophage/FASTQ_downloads

for sample in SRR21526559 SRR21526560 SRR21526561 SRR8926870 SRR8926871 SRR8926872 SRR8926873 SRR8926874 SRR8926875 SRR8926876 SRR8926877 SRR8926878
do
  echo "Aligning $sample ..."
  
  ~/rnaseq/tools/STAR-2.7.11b/bin/Linux_x86_64_static/STAR \
    --runThreadN 8 \
    --genomeDir ~/rnaseq/genome/STARindex \
    --readFilesIn /mnt/c/Users/pc/Desktop/RNAseq/Macrophage/FASTQ_downloads/$sample/${sample}_1.fastq \
                 /mnt/c/Users/pc/Desktop/RNAseq/Macrophage/FASTQ_downloads/$sample/${sample}_2.fastq \
    --outFileNamePrefix /mnt/c/Users/pc/Desktop/RNAseq/Macrophage/FASTQ_downloads/$sample/${sample}_ \
    --outSAMtype BAM SortedByCoordinate
done

```
  
