
<!-- README.md is generated from README.Rmd. Please edit that file -->
Código e base de dados
======================

Este repositório contém o código e a base de dados utilizados na análise de dados da tese de Lívia Pitelli. Nele você vai encontrar os seguintes arquivos e pastas.

Procedimento
------------

Há quatro arquivos denominados procedimento com diferentes extenções: Rmd, md, html e docx. O Rmd gera os demais e contêm os códigos utilizados para coletar as decisões do STF, limpá-las, organizá-las e realizar a análise exploratória.

Modelagem
---------

Este segue a mesma estrutura do primeiro e lhe dá continuidade. Nele estão as análises inferenciais e preditivas.

Data
----

Na pasta data, você encontra duas bases de dados em três formatos distintos: rds, json e xlsx. A base rcl\_dataset é a base final após a coleta e organização dos dados. Ela possui linhas a mais porque nela estão as decisões colegiadas.

A outra base é denominada rcl\_dataset\_ml. Ela é a base pronta para rodar regressão logística e os modelos de aprendizado de máquina.

Gráficos
--------

Nesta pasta constam os gráficos gerados durante a análise

Tabela
------

Nesta pasta constam as tabelas geradas a partir das duas principais bases.

Reproducibilidade
-----------------

A fim de assegurar a reproducibilidade, abaixo segue a configuração da máquina utilizada para realizar a coleta, organização e análise dos dados.

``` r
sessionInfo()
```

    #> R version 3.5.3 (2019-03-11)
    #> Platform: x86_64-pc-linux-gnu (64-bit)
    #> Running under: Ubuntu 18.04.2 LTS
    #> 
    #> Matrix products: default
    #> BLAS: /usr/lib/x86_64-linux-gnu/openblas/libblas.so.3
    #> LAPACK: /usr/lib/x86_64-linux-gnu/libopenblasp-r0.2.20.so
    #> 
    #> locale:
    #>  [1] LC_CTYPE=pt_BR.UTF-8       LC_NUMERIC=C              
    #>  [3] LC_TIME=pt_BR.UTF-8        LC_COLLATE=pt_BR.UTF-8    
    #>  [5] LC_MONETARY=pt_BR.UTF-8    LC_MESSAGES=pt_BR.UTF-8   
    #>  [7] LC_PAPER=pt_BR.UTF-8       LC_NAME=C                 
    #>  [9] LC_ADDRESS=C               LC_TELEPHONE=C            
    #> [11] LC_MEASUREMENT=pt_BR.UTF-8 LC_IDENTIFICATION=C       
    #> 
    #> attached base packages:
    #> [1] stats     graphics  grDevices utils     datasets  methods   base     
    #> 
    #> other attached packages:
    #>  [1] readxl_1.3.0         forcats_0.4.0        stringr_1.4.0       
    #>  [4] dplyr_0.8.0.1        purrr_0.3.2          readr_1.3.1         
    #>  [7] tidyr_0.8.3          tibble_2.1.1         ggplot2_3.1.0       
    #> [10] tidyverse_1.2.1.9000
    #> 
    #> loaded via a namespace (and not attached):
    #>  [1] nlme_3.1-137          bitops_1.0-6          fs_1.2.6             
    #>  [4] usethis_1.4.0         lubridate_1.7.4       httr_1.4.0           
    #>  [7] rprojroot_1.3-2       gh_1.0.1              tools_3.5.3          
    #> [10] backports_1.1.3       utf8_1.1.4            R6_2.4.0             
    #> [13] rpart_4.1-13          KernSmooth_2.23-15    lazyeval_0.2.2       
    #> [16] colorspace_1.4-1      nnet_7.3-12           withr_2.1.2          
    #> [19] tidyselect_0.2.5      gridExtra_2.3         git2r_0.24.0         
    #> [22] compiler_3.5.3        extrafontdb_1.0       cli_1.1.0            
    #> [25] rvest_0.3.2           xml2_1.2.0            caTools_1.17.1.2     
    #> [28] scales_1.0.0          AUC_0.3.0             digest_0.6.18        
    #> [31] rmarkdown_1.11        ini_0.3.1             pkgconfig_2.0.2      
    #> [34] htmltools_0.3.6       extrafont_0.17        rlang_0.3.1          
    #> [37] rstudioapi_0.9.0      generics_0.0.2        jsonlite_1.6         
    #> [40] gtools_3.8.1          ModelMetrics_1.2.2    magrittr_1.5         
    #> [43] Matrix_1.2-16         Rcpp_1.0.1            munsell_0.5.0        
    #> [46] fansi_0.4.0           whisker_0.3-2         stringi_1.4.3        
    #> [49] yaml_2.2.0            MASS_7.3-51.1         gplots_3.0.1.1       
    #> [52] plyr_1.8.4            recipes_0.1.4         grid_3.5.3           
    #> [55] gdata_2.18.0          crayon_1.3.4          lattice_0.20-38      
    #> [58] haven_2.1.0           splines_3.5.3         hms_0.4.2            
    #> [61] knitr_1.22            pillar_1.3.1          stf_0.0.0.9206       
    #> [64] clisymbols_1.2.0      reshape2_1.4.3        codetools_0.2-16     
    #> [67] stats4_3.5.3          glue_1.3.1            evaluate_0.13        
    #> [70] data.table_1.12.0     modelr_0.1.4          png_0.1-7            
    #> [73] foreach_1.4.4         Rttf2pt1_1.3.7        cellranger_1.1.0     
    #> [76] gtable_0.2.0          assertthat_0.2.0      xfun_0.5             
    #> [79] gower_0.2.0           prodlim_2018.04.18    broom_0.5.1          
    #> [82] rsconnect_0.8.13      class_7.3-15          survival_2.43-3      
    #> [85] timeDate_3043.102     iterators_1.0.10      classifierplots_1.3.3
    #> [88] writexl_1.1           lava_1.6.5            caret_6.0-81         
    #> [91] ROCR_1.0-7            ipred_0.9-8           here_0.1
