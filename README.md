# AMAC003_gsmn_for_simulations
Genome-scale metabolic network analysis of chicken microbiota for microbiome simulations.

# Relevant files

- **snakefile:** pipeline for generating genome-scale metabolic networks.
- **seeds.sbml:** gut microbiota seed file for GSMN analyses.

# Pipeline

## 1- Generate GSMNs
Generate genome-scale metabolic networks from MAG genomes using PathwayTools and Metage2Metabo.

```sh
# Clone this repository
git clone https://github.com/3d-omics/AMAC003_gsmn_for_simulations.git
cd AMAC003_gsmn_for_simulations

# Create a screen session
screen -S AMAC003

# Load required modules
module purge && module load snakemake/7.20.0 mamba/1.3.1

# Create mag folder
mkdir mag_catalogue

# Add all MAG sequences to the mag_catalogue folder, with .fa extensions
wget [MAG files]

# Run snakemake pipeline
snakemake \
  -j 50 \
  --cluster 'sbatch -o logs/{params.jobname}-slurm-%j.out --mem {resources.mem_gb}G --time {resources.time} -c {threads} --job-name={params.jobname} -v' \
  --use-conda --conda-frontend mamba --conda-prefix conda \
  --latency-wait 600
```

## 2- Generate community scopes
```sh
# Creat sbatch executable
cat <<EOF > community_scopes.sh
#!/bin/bash
#SBATCH --job-name=community_scopes
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mail-user=antton.alberdi@sund.ku.dk
#SBATCH --mem=128gb
#SBATCH --time=4:00:00
mkdir scopes
module load pathway-tools/27.0 blast/2.13.0 metage2metabo/1.5.3
m2m iscope -n genomes -s seeds.sbml -o scopes
EOF

#Launch sbatch
sbatch community_scopes.sh
```
