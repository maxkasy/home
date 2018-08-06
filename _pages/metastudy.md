---
layout: splash
title: Estimating publication bias using meta-studies
permalink: /metastudy/
---

## Estimating publication bias using meta-studies

*THIS IS A PRELIMINARY VERSION OF THE APP. IF YOU NOTE ANY PROBLEMS, PLEASE LET ME KNOW!*

This app allows you to estimate models of selective publication using meta-studies.
The estimated models give the probability of publication given the z-statistic, as well as the distribution of true effects among all studies (published and unpublished).
To use this app:

1. Upload a .csv file of your meta-study, with estimates in the first column and standard errors in the second column. Omit column headers and use comma-separated format.  
2. Choose a step-function model for p: Symmetric in Z or not, and cutoffs corresponding to critical values for two-sided tests at the 10%, 5%, and 1% levels.
3. Choose a model for the distribution true effects. For now, this app allows normal distributions and t-distributions.
4. Estimate the model by pressing the button.

To read more about the theoretical background, go to [PublicationBias.pdf](/files/papers/PublicationBias.pdf).  
The source code for the app can be downloaded at [https://github.com/maxkasy/MetaStudiesApp](https://github.com/maxkasy/MetaStudiesApp).   
An example dataset can be found here: [cleaned_minwage_data.csv](/files/other/cleaned_minwage_data.csv). These are data from Wolfson and Belman (2015), who conducted a meta-analysis of studies on the elasticity of employment with respect to the minimum wage.



<iframe src="https://maxkasy.shinyapps.io/MetaStudiesApp/" style="border:none;width:1000px;height:1200px;"></iframe>




