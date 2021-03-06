
```
TEMPLATE_16S=/data/bi/pipelines/TEMPLATES/16S_TEMPLATES
```
```
mkdir ANALYSIS DOC RAW REFERENCES RESULTS TMP

cd ANALYSIS
cp $TEMPLATE_16S/ANALYSIS/lablog .
bash lablog

cd *_ANALYSIS01_16S
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/lablog .
bash lablog

conda activate quality_control

cd 01-fastQC_previous
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/01-fastQC_previous/lablog .
bash lablog
bash _01_fastqc_pre_trimming.sh
mkdir logs
mv PRE_FQC* logs

cd ../02-cutadapt_remove_primers
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/02-cutadapt_remove_primers/lablog .
bash lablog
bash _01_cutadapt.sh
mkdir logs
mv CUTADPT* logs

cd ../03-fastp_preprocess
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/03-fastp_preprocess/lablog .
bash lablog
bash _01_fastp.sh
mkdir logs
mv FASTP* logs

cd ../04-fastQC_posterior
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/04-fastQC_posterior/lablog .
bash lablog
bash _01_fastqc_post_trimming.sh
mkdir logs
mv POST_FQC* logs

conda activate qiime2021.2

cd ../05-qiime2_import
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/05-qiime2_import/lablog .
bash lablog
bash _01_qiime2_import.sh

bash _02_qiime2_import_visualfile.sh
mkdir logs
mv Q2* logs

cd ../06-qiime2_denoise_cluster
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/06-qiime2_denoise_cluster/lablog .
bash lablog
bash _01_qiime2_dada2.sh

bash _02_qiime2_dada2_visualfiles.sh
mkdir logs
mv Q2* logs

cd ../07-qiime2_taxonomy
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/07-qiime2_taxonomy/lablog .
bash lablog
bash _01_qiime2_taxonomic_identification.sh

bash _02_qiime2_taxonomic_visual_export.sh
mkdir logs

cd ../08-qiime2_filt_mit_chl/
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/08-qiime2_filt_mit_chl/lablog .
bash lablog
bash _01_qiime2_filter_table_repseqs.sh

bash _02_qiime2_filtered_visualfiles.sh
mkdir logs
mv Q2* logs

cd ../09-qiime2_taxabarplots/01-Full
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/09-qiime2_taxabarplots/01-Full/lablog .
bash lablog
bash _01_qiime2_taxa_barplots_full.sh
mkdir logs
mv Q2* logs

cd ../02-Filtered
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/09-qiime2_taxabarplots/02-Filtered/lablog .
bash lablog
bash _01_qiime2_filter_taxa_barplots.sh
mkdir logs
mv Q2* logs

cd ../../10-qiime2_absoluteNumbers/01-Full
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/10-qiime2_absoluteNumbers/01-Full/lablog .
bash lablog
bash _01_qiime2_collapse_table.sh
bash _02_qiime2_export_biom_table.sh
bash _03_qiime2_biom_to_tsv.sh
bash _04_python_transpose_tsv.sh
bash _05_python_core.sh
bash _06_python_heatmaps.sh
mkdir logs
mv Q2* logs
mv PY* logs

cd ../02-Filtered
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/10-qiime2_absoluteNumbers/02-Filtered/lablog .
bash lablog
bash _01_qiime2_collapse_table.sh
bash _02_qiime2_export_biom_table.sh
bash _03_qiime2_biom_to_tsv.sh
bash _04_python_transpose_tsv.sh
bash _05_python_core.sh
bash _06_python_heatmaps.sh
mkdir logs
mv Q2* logs
mv PY* logs

cd ../../11-qiime2_phylogeny/01-Full
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/11-qiime2_phylogeny/01-Full/lablog .
bash lablog
bash _01_qiime2_align.sh
bash _02_qiime2_export_tree.sh
mkdir logs
mv Q2* logs

cd ../02-Filtered
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/11-qiime2_phylogeny/02-Filtered/lablog .
bash lablog
bash _01_qiime2_align_filtered.sh
bash _02_qiime2_export_tree.sh
mkdir logs
mv Q2* logs

cd ../../12-qiime2_diversity/01-Full
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/12-qiime2_diversity/01-Full/lablog .
bash lablog SEQUENCING_DEPTH_FOR_FULL_SAMPLES
bash _01_qiime2_alpha_diversity_full.sh
bash _02_qiime2_alpha_diversity_visual.sh
mkdir logs
mv Q2* logs

cd ../02-Filtered
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/12-qiime2_diversity/02-Filtered/lablog .
bash lablog SEQUENCING_DEPTH_FOR_FILTERED_SAMPLES
bash _01_qiime2_alpha_diversity_filtered.sh
bash _02_qiime2_alpha_diversity_visual.sh 
mkdir logs
mv Q2* logs

cd ../../13-qiime2_rarefaction/01-Full
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/13-qiime2_rarefaction/01-Full/lablog .
bash lablog SEQUENCING_DEPTH_FOR_FULL_SAMPLES
bash _01_qiime2_alpha_rarefaction.sh
mkdir logs
mv Q2* logs

cd ../02-Filtered
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/13-qiime2_rarefaction/02-Filtered/lablog .
bash lablog SEQUENCING_DEPTH_FOR_FILTERED_SAMPLES
bash _01_qiime_alpha_rarefaction_filtered.sh
mkdir logs
mv Q2* logs

cd ../../14-qiime2_gneiss_differential_abundance/01-Full
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/14-qiime2_gneiss_differential_abundance/01-Full/lablog .
bash lablog
bash _01_qiime_gneiss_build_hierarchy_full.sh
bash _02_qiime_gneiss_heatmap_full.sh
mkdir logs
mv Q2* logs

cd ../02-Filtered
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/14-qiime2_gneiss_differential_abundance/02-Filtered/lablog .
bash lablog
bash _01_qiime_gneiss_build_hierarchy_filtered.sh
bash _02_qiime_gneiss_heatmap_filtered.sh
mkdir logs
mv Q2* logs

cd ../../15-qiime2_ANCOM/01-Full
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/15-qiime2_ANCOM/01-Full/lablog .
bash lablog
bash _01_qiime_pseudocount_full.sh
bash _02_qiime_ancom_full.sh
bash _03_qiime_export_ancom_full.sh
bash _04_python_get_full_abundance_tables.sh
mkdir logs
mv Q2* logs
mv PY* logs

cd ../02-Filtered
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/15-qiime2_ANCOM/02-Filtered/lablog .
bash lablog
bash _01_qiime_pseudocount_filt.sh
bash _02_qiime_ancom_filt.sh
bash _03_qiime_export_ancom_filt.sh 
bash _04_python_get_filt_abundance_tables.sh
mkdir logs
mv Q2* logs
mv PY* logs

conda activate quality_control

cd ../../99-stats
cp $TEMPLATE_16S/ANALYSIS/ANALYSIS01_16S/99-stats/lablog .
bash lablog
bash _01_multiqc.sh
bash _02_read_balance.sh
mkdir logs
mv MULTIQC* logs
mv READ_BALANCE* logs
```

