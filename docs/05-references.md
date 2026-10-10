# References

This list is limited to resources explicitly named or linked in the *CASPER Toolflow Environment Setup* PDF, plus the AMD support thread separately supplied for the MATLAB/System Generator initialization issue.

## CASPER toolflow and source repositories

1. **CASPER Toolflow documentation**  
   https://casper-toolflow.readthedocs.io

2. **CASPER `mlib_devel` GitHub repository**  
   https://github.com/casper-astro/mlib_devel

3. **CASPER mailing-list archive: Vitis backend and device-tree generation**  
   https://www.mail-archive.com/casper@lists.berkeley.edu/msg09031.html  
   Linked in the PDF's Vitis backend section; relevant to `JASPER_BACKEND=vitis` and `XLNX_DT_REPO_PATH`.

4. **CASPER mailing-list archive: ZCU216 engineering-sample / production-silicon issue (1)**  
   https://www.mail-archive.com/casper@lists.berkeley.edu/msg08900.html

5. **CASPER mailing-list archive: ZCU216 engineering-sample / production-silicon issue (2)**  
   https://www.mail-archive.com/casper@lists.berkeley.edu/msg09150.html

## Installation and System Generator troubleshooting

6. **StrathSDR: System Generator on Ubuntu 20.04 with Vivado 2021**  
   https://strath-sdr.github.io/tools/matlab/sysgen/vivado/linux/2021/01/28/sysgen-on-20-04.html

7. **AMD Adaptive Support: Model Composer 2021.2 / MATLAB R2021a initialization hang on Ubuntu 20.04.1**  
   https://adaptivesupport.amd.com/s/question/0D52E00006vF6FOSA0/model-composer-v20212-matlab-r2021a-gets-stuck-at-initialization-stage-on-ubuntu-20041?language=en_US  
   Supplied separately and relevant to the initialization issue. The thread title refers to Model Composer 2021.2, while this guide documents 2021.1; treat it as related context, not an exact version match.

## Additional community installation references linked in the PDF

8. **CASPER installation notes (GitLab, University of Turku)**  
   https://gitlab.utu.fi/kjwiik/casper-installation

9. **CASPER installation gist**  
   https://gist.github.com/dcxSt/13f0760ee423082f15e151170b943fa6

## Citation practice

- Cite the Vitis/device-tree mailing-list thread where the Vitis backend and `XLNX_DT_REPO_PATH` are discussed.
- Cite both ES1 mailing-list threads where the ZCU216 platform-part mismatch is discussed.
- Cite StrathSDR and the AMD support thread in the MATLAB/System Generator initialization troubleshooting section.
- Use the CASPER Toolflow documentation and `mlib_devel` repository for the general toolflow setup.
- The PDF shows two links labelled “Github references used” but the extracted text does not expose their target URLs. Their destinations have not been guessed.