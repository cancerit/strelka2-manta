# strelka2-manta
This repo holds a Dockerfile for the Strelka2 germline/somatic variant caller and the Manta structural variant caller. Its only intended for deployment purposes of the image. Strelka2 and Manta are Illumina software so no direct CASM support is given for them.

Image deploys Manta v1.6.0 & Strelka2 v2.9.10

Information for Manta can be found [ here ](https://github.com/Illumina/manta)

Information for Strelka2 can be found [ here ](https://github.com/Illumina/strelka)

Typical execution of the container with singularity in the cluster is carried out:

`module load singularity/3.6.4`

`singularity pull docker://quay.io/wtsicgp/strelka2-manta`

`singularity exec --cleanenv --bind /lustre:/lustre:ro --bind /nfs:/nfs:ro  strelka2-manta_latest.sif runMantaWorkflowDemo.py`
