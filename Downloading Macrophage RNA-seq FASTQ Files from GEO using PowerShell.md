# A reproducible workflow using SRA Toolkit on Windows

## This guide walks through downloading raw FASTQ files (paired-end RNA-seq) from the GEO dataset GSE130011, which includes human macrophage activation states:

<img width="760" height="368" alt="image" src="https://github.com/user-attachments/assets/754c17c6-d0be-4aac-be30-98c689ced27e" />


### The link to the paper: https://pmc.ncbi.nlm.nih.gov/articles/PMC10902381/?utm_source=chatgpt.com#CR76

## Step 1: Install SRA tool kit
- Check out the github page: https://github.com/ncbi/sra-tools/wiki/01.-Downloading-SRA-Toolkit
- Make sure tools are added to PATH:

## Step 2: download metadata
- For instance: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE130011
- Go to `SRA Run Selector`
- Select the files you need and then download `metadata`
<img width="1831" height="635" alt="image" src="https://github.com/user-attachments/assets/ddb003de-d693-41ff-9a56-d55c7e6f5f2d" />

## Step 3: Create SRR_list.txt
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
# PowerShell script to download SRA runs and convert to FASTQ

# Folder to save FASTQs
$OutDir = "C:\Users\pc\Desktop\RNAseq\FASTQ_downloads"
New-Item -ItemType Directory -Force -Path $OutDir | Out-Null

# Read SRR list
$SRRs = Get-Content "C:\Users\pc\Desktop\RNAseq\SRR_list.txt"

foreach ($SRR in $SRRs) {
    Write-Host ""
    Write-Host "=== Processing $SRR ==="

    # 1️⃣ Download .sra files
    Write-Host "Downloading SRA file..."
    prefetch $SRR --output-directory $OutDir

    # Path created by prefetch
    $SRAfile = Join-Path $OutDir "$SRR\$SRR.sra"

    # 2️⃣ Convert to FASTQ (Paired-end expected)
    Write-Host "Converting to FASTQ..."
    fasterq-dump $SRAfile --split-files --outdir $OutDir

    # 3️⃣ Compress FASTQs if present
    if (Test-Path "$OutDir\${SRR}_1.fastq") {
        gzip "$OutDir\${SRR}_1.fastq"
    }
    if (Test-Path "$OutDir\${SRR}_2.fastq") {
        gzip "$OutDir\${SRR}_2.fastq"
    }

    Write-Host "$SRR done."
}

Write-Host ""
Write-Host "All downloads finished!"

```

## Step 5: Run the script
```
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\download_fastq.ps1
```
