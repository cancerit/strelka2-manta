# strelka2-manta
This repo holds a Dockerfile for the Strelka2 germline/somatic variant caller and the Manta structural variant caller. Its only intended for deployment purposes of the image. Strelka2 and Manta are Illumina software so no direct CASM support is given for them.

Image deploys Manta v1.6.0 & Strelka2 v2.9.10

Information for Manta can be found [ here ](https://github.com/Illumina/manta)

Information for Strelka2 can be found [ here ](https://github.com/Illumina/strelka)

Downloading image from quay.io

```
#download the image (sif file)
module load singularity/3.6.4
singularity pull docker://quay.io/wtsicgp/strelka2-manta
```
Running the demo
```
singularity exec --cleanenv --bind /lustre:/lustre:ro --bind /nfs:/nfs:ro  strelka2-manta_latest.sif \
  runMantaWorkflowDemo.py
```
Running a typical somatic variant workflow
```
CPUS=8

GENOME=/lustre/scratch119/casm/team78pipelines/canpipe/live/ref/human/GRCH37d5/genome.fa
MANTA_ANALYSIS_PATH=./mantaAnalysis
STRELKA_ANALYSIS_PATH=./strelkaAnalysis
TUMOR=PD22a.bam
NORMAL=PD22b.bam

#run Manta
singularity exec --cleanenv --bind /lustre:/lustre:ro --bind /nfs:/nfs:ro strelka2-manta_latest.sif \
  configManta.py \
  --normalBam $NORMAL \
  --tumorBam $TUMOR \
  --referenceFasta $GENOME \
  --runDir ${MANTA_ANALYSIS_PATH}

singularity exec --cleanenv --bind /lustre:/lustre:ro --bind /nfs:/nfs:ro strelka2-manta_latest.sif \
  ${MANTA_ANALYSIS_PATH}/runWorkflow.py -j $CPUS

#run Strelka
singularity exec --cleanenv --bind /lustre:/lustre:ro --bind /nfs:/nfs:ro strelka2-manta_latest.sif \
  configureStrelkaSomaticWorkflow.py \
  --normalBam $NORMAL \
  --tumorBam $TUMOR \
  --referenceFasta $GENOME \
  --indelCandidates ${MANTA_ANALYSIS_PATH}/results/variants/candidateSmallIndels.vcf.gz \
  --runDir ${STRELKA_ANALYSIS_PATH}

singularity exec --cleanenv --bind /lustre:/lustre:ro --bind /nfs:/nfs:ro strelka2-manta_latest.sif \
  ${STRELKA_ANALYSIS_PATH}/runWorkflow.py -m local -j $CPUS
```
