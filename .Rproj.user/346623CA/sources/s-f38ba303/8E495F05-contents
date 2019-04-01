---
title: "Análise das decisões do STF sobre reclamação"
author: "Livia Houaiss"
date: "1/2/2019"
params:
  gdrive_folder_url: "https://drive.google.com/drive/u/1/folders/1FroD2-OhOBqYvTH4yd6oqfDRl_O3YU1h"
output:
  html_document:
    keep_md: true
    theme: simplex
  #  highlight: monochrome
    fig.caption: yes  
bibliography: biblio.bib
link-citations: yes
nocite: | 
  @item1, @item2
---





# Introdução

O procedimento abaixo percorre o caminho para baixar, limpar e organizar as decisões do Supremo Tribunal Federal sobre a ação de reclamação. Para realizar este procedimento, utilizou-se um conjunto de rotinas de computador. Esse conjunto de rotinas foi criado dentro da linguagem de programação estatística [R](https://www.r-project.org/). A cada uma das rotinas é dado um nome. Esses nomes são agrupados no aplicativo conhecido como `package` (pacote de funções) de uma determinada linguagem de programação. A esse  pacote também é dado um nome, que no caso se chama [stf](https://jjesusfilho.github.io/stf/). Uma vez incorporadas num aplicativo, as rotinas, doravante chamadas de funções,  podem ser facilmente reutilizadas, bastando chamá-las pelo nome e informar os argumentos para sua execução dentro de parenteses. 

O pacote construído a partir da presente pesquisa assegura replicabilidade e reproducibilidade. As pesquisas científicas atuais, especialmente as quantitativas, caminham no sentido de garantir a reproducibilidade, isto é, o caminho percorrido pelo pesquisador no processo de coleta, organização, exploração e análise dos dados, pode ser reproduzido por qualquer outro pesquisador que tenha familiaridade com o programa utilizado. Reproducibilidade significa usar os mesmos dados e a mesma análise (códigos e modelos) e chegar aos mesmos resultados. Replicabilidade significa aproveitar o mesmo método (código e análise) para aplicá-los a novos dados.

O `R` é uma linguagem de programação de código aberto e gratuita. Igualmente, o pacote stf, criado ao londo desta pesquisa, é livre acesso. Para acessá-lo, basta clicar no seguinte [link](https://github.io/jjesusfilho/stf). Ali se encontram as orientações sobre como utilizá-lo. 

O [pacote stf](https://github.io/jjesusfilho/stf) foi construído pensando não somente em viabilizar a presente pesquisa, mas também em oferecer a acadêmicos de direito, de estatística e de ciência da computação, ferramentas para condução de suas análises sobre a atuação do Supremo Tribunal Federal. Nesse sentido, entendemos que este é um valioso subproduto da presente pesquisa de doutorado.


## Pacotes necessários


```r
install.packages(c("devtools","tidyverse","janitor","quanteda"))


devtools::install_github("jjesusfilho/stf")
library(stf)
library(tidyverse)
library(janitor)
```


## Baixar o acervo do STF

O acervo de decisões do STF é composto por três grupos de decisões: monocráticas, correspondentes às decisões individuais dos ministros; colegiadas, correspondentes às decisões das turmas e do pleno; presidente, correspondentes as decisões do presidente.  A função abaixo irá baixar todo o acervo de decisões do STF correspondente aos anos indicados e o tipo de decisão.


```r
download_stf_collection(decision_type = "monocraticas",years = 2011:2018,dir = "monocraticas")
download_stf_collection(decision_type = "colegiadas",years = 2011:2018,dir = "colegiadas")
download_stf_collection(decision_type = "presidente",years = 2011:2018,dir = "presidente")
```

## Ler o acervo

A função abaixo importa o acervo conforme a classe processuale os anos indicados. Esta função já faz o trabalho inicial de limpar a base de alguns elementos desnecessários e criar uma coluna chamada "incidente", que é extraída do hyperlink.


```r
monocraticas <- read_stf_collection(classes = "Rcl",years = 2011:2018,dir = "monocraticas")
colegiadas <- read_stf_collection(classes = "Rcl",years = 2011:2018,dir = "colegiadas")
presidente <- read_stf_collection(classes = "Rcl",years = 2011:2018,dir = "presidente")
```


## Junção das bases

Antes de seguir para os próximos passos, temos de juntar essas três bases e selecionar os processos únicos, de modo a reduzir o número de requisições de processos nos passos seguintes.


```r
acervo <- bind_rows(monocraticas,colegiadas, presidente)

numeros <- unique(acervo$numero)
```

Os números revelam que houve 22532 reclamações julgadas pelo Supremo Tribunal Federal entre janeiro de 2011 e dezembro de 2018.

## Remover colunas não utilizadas

Para esta análise específica, somente algumas colunas são de interesse. O procedimento abaixo seleciona tais colunas.


```r
acervo <- acervo %>% 
  select(classe,numero,data_autuacao,relator_atual,tipo_decisao,orgao_julgador,data_andamento)
```


## Baixar os processos

A maneira mais rápida de baixar os processos seria por meio da coluna incidente. No entanto, notou-se que nem sempre o `hiperlink`, do qual é extraído o número do incidente, existe. Dessa forma, optou-se utilizar o número do processo na busca. A função abaixo realiza esta busca no portal do STF.

Esta busca poderá demorar bastante tempo porque são necessárias múltiplas requisições para cada um dos processos,  correspondentes aos detalhes básicos que aparecem no topo das informações processuais e às oito abas.

Esta função irá criar nove pastas dentro do diretório indicado, correspondentes às oito abas mais a pasta com os detalhes (metadados). Veja que para baixar esses arquivos, é necessário ter lido o acervo antes, pois utilizaremos as colunas classe e número para baixá-los.


```r
download_stf_dockets(classes = "Rcl",docket_number = numeros)
```

## Lendo as informações processuais.

Das nove pastas, quatro delas são de especial interesse: detalhes, andamentos, partes e informações. Para a nossa análise, as demais são dispensáveis.


```r
detalhes <- read_stf_details(path = "detalhes", plan = "multicore")

andamentos <- read_stf_docket_sheet(path = "andamentos", plan = "multicore")

informacoes <- read_stf_information(path = "informacoes", plan = "multicore")

partes <- read_stf_parties(path = "partes", plan = "multicore")
```


## Delimitação do escopo

Conforme explicitado no desenho metodológico, a presente pesquisa visa analisar as decisões de mérito do Supremo Tribunal Federal em casos com trânsito em julgado sobre pedidos de reclamação contra órgãos jurisdicionais entre os anos de 2011 e 2012, com exceção daquelas que versam sobre a competência do STF. Para realizar esse filtro, é necessária a utilização tanto de automação, vez que são mais de 22 mil processos, como também de métodos manuais, para aqueles casos em que a automação não for suficiente para extrair as informações relevantes.

Sobre o uso de métodos automatizados de mineração e classificação de texto, observa-se que textos, nesse caso, decisões judiciais, são dados não estruturados. Com os avanços dos processos de automação na leitura e extração de dados, bem como, dos processos de classificação por meio do uso de inteligência artificial (deep learning), surgiram possibilidades de análise que antes não eram possíveis.

No entanto, o uso de computação para ler e classificar textos está longe de atingir a qualidade do processo humano. Como bem afirmam [@grimmer2013text], os métodos quantitativos de classificação de texto ampliam a atividade humana, não a substituem. Além disso, não existe um método global de análise automatizada. Por fim, a classificação automatizada deve ser validada por meio de revisão humana. A técnica adotada é tirar uma amostra de algumas dezenas de casos e submeter à leitura humana para verificar se houve erros e, em caso afirmativo, se o número é muito grande.

## Remoção dos casos que não serão analisados.

Primeiramente iremos remover três grupos de casos. Aqueles que não transitaram em julgado no momento da análise, aqueles que correm em segredo de justiça e aqueles cujo reclamado não é órgão judicial. A informação sobre o trânsito em julgado se encontra no andamento. A informação sobre o segredo de justiça se encontra nos detalhes.


```r
transitado <- andamentos %>% 
  filter(str_detect(titulo,"(?i)transitado")) %>% 
  pull("incidente") %>% 
  unique()

publico <- detalhes %>% 
  filter(!str_detect(sigilo,"Segredo de Justiça")) %>% 
  pull("incidente") %>% 
  unique()
```

Para manter somente os casos em que o reclamado é órgão judicial, utilizaremos a função `classify_respondent()` do pacote `stf` . Além de excluir os casos em que o respondente não é órgão judicial, ela classifica o órgão judicial conforme a instância e o segmento do Poder Judiciário ao qual pertence.

A classificação é bem sucedida na maioria dos casos, porém alguns não foram atingidos. 


```r
partes <- classify_respondent(partes)

remover_conselhos <- conselhos %>%
  select(c(1,4,5)) %>%
  slice(38:57) %>%
  setNames(c("reclamados","incidente","acao")) %>%
  filter(acao=="EXCLUIR")


remover_conselhos[2,1]<-"CONSELHO DA MAGISTRATURA DO TRIBUNAL DE JUSTIÇA DO ESTADO DO RIO GRANDE DO SUL"

partes <- partes %>%
  dplyr::filter(!is.element(incidente,remover_conselhos$incidente),!is.element(reclamado,remover_conselhos$reclamados))

partes <- partes %>%
  filter(instancia!="outros") %>%
  filter(segmento!="outros")
```

Por fim, verificamos que na base andamentos, há movimentações que foram invalidadas. A função de leitura dos andamentos cria uma coluna indicando se a movimentação foi invalidada ou não. O que temos de fazer é simplesmente remover tais linhas da base andamentos.


```r
andamentos <- andamentos %>% 
  filter(!invalido)
```


## Baixar textos das decisões

Para baixar os textos das decisões, criamos duas funções, uma que baixa os textos em rtf, outra que baixa os textos em pdf. As urls dos textos estão contidas na base andamentos. Acontece que nem todos os textos se referem a decisões de mérito. Alguns são pareceres do Ministério Público, outros são meros despachos, outros são decisões interlocutórias ou apreciação de liminar ou cautelar. 
No entanto, a base andamento nem sempre é suficiente informativa a respeito. Por essa razão, optamos por baixar todos os textos e, posteriormente, realizar as exclusões.


```r
dir.create("textos_rtf")
dir.create("textos_pdf")
stf::download_stf_pdf(andamentos,"textos_pdf")
stf::download_stf_rtf(andamentos,"textos_rtf")

textos_rtf <- stf::read_stf_rtf("textos_rtf")
textos_pdf <- stf::read_stf_pdf("textos_pdf")
texto <- bind_rows(textos_rtf,textos_pdf)
```

## Limpeza dos textos
Os textos das decisões são usados para identificar casos que devem ser excluídos. Por exemplo, processos em que não houve decisão de mérito, tais como decisões terminativas sem julgamento de mérito, processos extintos e prejudicados seja por inércia da parte, seja por perda do objeto.

Além disso, a decisão contida na base acervo nem sempre corresponde à real decisão sobre reclamação, pois os técnicos do STF simplesmente extraem a última decisão do andamento, a qual por vez é um mero despacho. Igualmente o título do andamento nem sempre é suficientemente informativo. Por vezes, apenas indicava que se tratatava de uma publicação no diário de justiça.

Por essa razão, optou-se por extrair dos próprios textos a parte que corresponde à decisão, isto é, extraímos os últimos 800 caracteres do texto, que provavelmente contêm a decisão.  Antes, porém, excluímos do texto as informações sobre quem assinou o documento.

Ademais, cortamos essa parte a partir de palavras chave tais como ex positis, diante do exposto etc. Com isso, foi possível verificar se a decisão se tratava de liminar ou cautelar, se era um mero despacho concedendo vista ou uma cerdidão. Algumas palavras no início do texto indicavam se ele era um parecer do MP, se era um embargo de declaração ou se era um agravo regimental. Quando não foi possível identificar no início, foi possível fazê-lo no final do texto.

Alguns embargos foram convertidos em agravo regimental. A identificação desses casos também foi necessária.


```r
texto <- texto %>%
  unite("docname",extensao,docid,sep="") %>%
  inner_join(andamentos,by="docname")

texto <- texto %>%
  mutate(caracteres=nchar(texto))


texto <- texto %>%
  mutate(texto=str_remove_all(texto,"(?i)documento assinado\\X+?(sob|por)\\s\\w+"))


texto <- texto %>%
  mutate(texto = str_squish(texto))



texto <- texto %>%
  mutate(dispositivo = case_when(
    caracteres > 800 ~{
      stringi::stri_reverse(texto) %>%
        stringi::stri_extract_first_regex("\\X{800}") %>%
        stringi::stri_reverse()
    },
    TRUE ~ texto))



texto <- texto %>%
  mutate(dispositivo=case_when(
    str_detect(texto1,"(?i)(antes? o exposto|ex positis|diante disso|ante o quadro|pelo exposto|diante do exposto|nesse contexto|diante do contexto|por es[st]a razão|pelas razões expostas|por todo o exposto|dessa forma|decis.o:|com essas considerações|nessas condições|sendo assim|face ao exposto|do exposto)") ~{
      str_extract(dispositivo,"(?i)(antes? o exposto|ex positis|diante disso|ante o quadro|pelo exposto|diante do exposto|nesse contexto|diante do contexto|por es[ts]a razão|pelas razões expostas|por todo o exposto|dessa forma|decis.o:|com essas considerações|nessas condições|sendo assim|face ao exposto|do exposto).+")
    },
    TRUE ~ dispositivo))


texto <- texto %>%
  filter(!str_detect(dispositivo,"(?i)defiro a liminar"))

texto <- texto %>%
  mutate(exclusoes = str_extract(texto,"\\X{80}")) %>%
  # filter(!str_detect(cautelar,"MEDIDA CAUTELAR")) %>%
  filter(!str_detect(exclusoes,"(?i)(^Documento|^MINISTÉRIO|^procurador)")) %>%
  filter(!str_detect(exclusoes,"EMB.DECL.")) %>%
  filter(!str_detect(doc,"(?i)(PGR|despacho|certid.o|vista)")|is.na(doc)) %>%
  select(-exclusoes)
```

## Classificação dos textos conforme provimento ou improvimento

O procedimento abaixo classifica os textos das decisões conforme provimento ou improvimento. O trabalho aqui foi recortar as decisões que eram de mérito. Para tanto, aplicamos a técnica de expressões regulares (regex) para identificar padrões nos textos dos dispositivos. 

Todo esse trabalho de identificação é heurístico e requer a conjunção de esforços de automação e validação pelo pesquisador. Uma técnica de processamento de linguagem natural muito útil é conhecida como "kwic" (key word in context). Por ela, buscamos uma palavra-chave, e.g., "procedente" em seu contexto, isto é, verificamos todas as vezes que esta palavra aparece no dispositivo e quais as palavras que a antecedem ou que a sucedem. Este procedimento permite observar padrões tais como "não procedente" ou "julgo procedente". A experiência com classificações em outras pesquisas tem nos permitido reduzir significativamente as chances de erro.

Mesmo quando ocorrem alguns erros, esses são mínimos e podem ser verificados por outros meios, tais como a verificação da consistência da base. Por exemplo, um texto erroneamente classificado como decisão de agravo deve ser contrastado com o tipo de decisão. Se esta foi uma decisão monocrática, claramente não se trata de um agravo. 

Por fim, uma particularidade  do STF é o uso da expressão "nego seguimento" ora par indicar "improcedência", ora para indicar uma decisão terminativa sem julgamento do mérito. Esse aspecto representou uma dificuldade a mais, pois foi necessário encontrar outras palavras que indicasse quando a expressão estava sendo usada para julgamento de mérito, e.g. "aderência" ou quando definitivamente não era de mérito, e.g, "súmula 734" ou "sucedâneo" e "atalho".

Diante da dúvida se a decisão era de mérito ou não, preferiu-se excluí-la. Ao final, depois de todas as exclusões e aplicações de filtros, chegou-se a 5636 casos. Uma amostra foi retirada para realização de validação humana. Não foi identificado nenhum erro. Isso não signfica que a base está isenta de erros de classificação, mas a pesquisadora está segura de que se estes ocorreram, eles foram mínimos e não afetarão significativamente a análise.

Ainda assim, a próxima etapa da análise, denominada "Exploratory Data Analysis" permite verificar inconsistências ou disparidades nas distribuições e anomalias nos dados. Novas correções são possíveis nesse momento.




```r
texto <- texto  %>%
  dplyr::mutate(decisao = stringi::stri_trans_tolower(dispositivo),
                decisao = abjutils::rm_accent(decisao),
                decisao = case_when(
                  str_detect(decisao,"(nego|negado|negou)\\sseguimento") ~ "nego seguimento",
                  str_detect(decisao,"(desprov\\w+|improv\\w+|improced\\w+)") ~ "improvido",
                  str_detect(decisao,"(nao|nega\\w+)\\s+provi.*")~ "improvido",
                  str_detect(decisao,"(rejeit\\w+|inadmitidos?)") ~ "improvido",
                  str_detect(decisao,"mantiveram") ~ "improvido",
                  str_detect(decisao,"(acolho|acolhido)") ~ "provido",
                  str_detect(decisao,"(deram|da\\-*\\s*se|dando\\-*(se)*|comporta|dou|confere\\-se|se\\s*\\-*da|merece)\\sprovi\\w+") ~ "provido",
                  str_detect(decisao,"parcial\\w*\\sprovimento") ~ "provido",
                  str_detect(decisao,"(nao\\sderam|nao\\smerece|se\\snega|nega\\-*\\s*se|negar\\-*\\s*lhe|nao\\scomporta|negram|negararam|nego|negar|negou)") ~ "improvido",
                  str_detect(decisao,"\\bprovimento") ~ "provido",
                  str_detect(decisao,"\\bprocedente") ~ "provido",
                  str_detect(decisao,"(nao\\sconhec\\w+|nao\\sse\\sconhec\\w+)") ~ "não conhecido",
                  str_detect(decisao,"desconh\\w+") ~ "desconhecido",
                  str_detect(decisao,"nao\\s+conhec\\w+") ~ "desconhecido",
                  str_detect(decisao,"(homolog|desistencia)") ~ "desistência",
                  str_detect(decisao,"diligencia") ~ "conversão em diligência",
                  str_detect(decisao,"sobrest") ~ "sobrestado",
                  str_detect(decisao,"prejudicad\\w*") ~ "prejudicado",
                  str_detect(decisao,"(anular\\w*|nulo|nula|nulidade)") ~ "anulado",
                  TRUE ~ "outros"))

texto <- texto %>%
  mutate(decisao = case_when(
    decisao == "provido" ~ "procedente",
    decisao == "improvido" ~ "improcedente",
    TRUE ~ decisao
  ))

improcedente <- texto %>%
  filter(decisao == "improcedente")

procedente <- texto %>%
  filter(decisao == "procedente")

seguimento <- texto %>%
  filter(decisao=="nego seguimento")


prejudicado<-texto %>%
  filter(decisao=="prejudicado/extinto")

sobrestado <- texto %>%
  filter(decisao=="sobrestado")


outros <- texto %>%
  filter(decisao=="outros")


sucedaneo <- seguimento %>%
  select(texto,docname) %>%
  quanteda::corpus("docname","texto") %>%
  quanteda::kwic("(?i)(suced[aâ]neo|\\bpresta\\b|atalho)",window = 20,valuetype = "regex") %>%
  as_tibble()

sucedaneo<-sucedaneo %>%
  filter(keyword!="presta-se") %>%
  pull("docname") %>%
  unique()

seguimento <- seguimento %>%
  filter(!docname %in% sucedaneo)


sumula_734 <- seguimento %>%
  filter(str_detect(texto,"\\b734\\b")) %>%
  pull("docname") %>%
  unique()

seguimento <- seguimento %>%
  filter(!docname %in% sumula_734)

aderencia <- seguimento %>%
  select(texto,docname) %>%
  quanteda::corpus("docname","texto") %>%
  quanteda::kwic("(?i)ader.ncia",window = 20,valuetype = "regex") %>%
  as_tibble()
```



# Análise exploratória de dados

Realizado o trabalho de coleta, limpeza e organização dos dados, a etapa seguinte é conduzir a análise exploratória de dados (EDA na sigla em inglês). A análise exploratória de dados visa dar a conhecer a estrutura subjacente dos dados e expor um conjunto de informações acerca dos dados a fim de que o pesquisador passa tomar passos adiante ou atrás no processo de análise. Igualmente, ela fornece ao leitor as principais informações acerca dos dados. Apoiando-se em [@pearson2018exploratory], a análise exploratória dos dados coletados sobre as reclamações ao STF busca responder as seguintes questões:

1. Quantos registros a base de dados sobre reclamações contêm? (Isto é, quantas decisões do STF sobre reclamações estão sendo analisadas)
2. Quantas colunas, i.e, variáveis, estão incluídas em cada um dos registros?
3. Que tipo de variáveis são essas? (i.e. numéricas, categóricas, contínuas, discretas?)
4. Esses dados foram todos observados? (i.e. há dados faltantes, há outliers?)
5. As variáveis incluídas na base são aquelas que nós realmente estávamos esperando?
6. Os valores contidos nas variáveis são consistentes?, i.e. número de categorias, categorias corretas etc, ?
7. As associações entre as variáveis são aquelas que esperávamos? 
7.1 Por exemplo, podemos esperar que o CPC 2015 elevou o número de procedência dos pedidos?
7.2 Podemos esperar diferenças entre os ministros quanto ao número de casos procedentes ou não?

É importante destacar que a análise exploratória é útil para verificar a associação entre as variáveis, particularmente entre as variáveis preditoras e a variável resposta. Inclusive iremos realizar alguns testes de associação, e.g, chi-quadrado, de força dessas associações (WOE) e mesmo capacidade preditiva das variáveis explicativas. No entanto, é fundamental tomar em conta que os testes de associação e de significância, tal como o teste do chi-quadrado e o t-test, não informam nada sobre o efeito marginal de cada uma das variáveis explicativas sobre a variável resposta. Em pesquisas experimentais, isso é perfeitamente possível porque o pesquisador possui controle preciso sobre os fatores que causam ou modificam o resultado obtido. Por sua vez, as pesquisas em ciências sociais se caracterizam por serem observacionais e a pesquisadora não possui controle apriorístico sobre o efeito de uma variável sobre a outra. Ela precisa precisa considerar outros fatores que afetam o resultado para assim isolar o efeito de cada uma das variáveis explicativas sobre o resultado[@silva2018desenho].

Na pesquisa em tela, estamos diante de dados observacionais. O esforço é dirigido em identificar todos os possíveis fatores que influenciam a resposta judicial ao pedido. Nesse sentido, os testes de associação bivariados são inadequados porque irão superdimensionar o efeito de uma variável sobre a outra. A tarefa, a qual será executada na próxima seção, será de isolar o efeito de cada uma das variáveis explicativas sobre a variável resposta: decisão judicial. Com efeito, podemos supor que o ministro A julgará diferentemente o pedido de reclamação quando se tratar de uma alegada violação a uma súmula do STF do que quando se tratar de uma violaçã a uma decisão inter partes ou que uma reclamação contra decisão da justiça do trabalho tem maior probabilidade de ter uma resposta favorável quando o ministro é fulano do que quando este é beltrano. Esse tipo de controle somente é posssível realizar por meio das técnicas de regressão, pois o objetivo destas é justamente isolar o efeito de cada uma das variáveis sobre os resultado.




## Estatísticas das variáveis

A tabela abaixo 

```r
rcl_dataset <- readRDS("../data/rcl_dataset.rds")
(
  descricao1 <- ExpData(rcl_dataset,1) %>% 
setNames(c("Descrições","Registros"))
)
```

```
##                                   Descrições Registros
## 1                         Sample size (Nrow)      6353
## 2                    No. of Variables (Ncol)         9
## 3                   No. of Numeric Variables         0
## 4                    No. of Factor Variables         8
## 5                      No. of Text Variables         0
## 6                   No. of Logical Variables         1
## 7                      No. of Date Variables         0
## 8   No. of Zero variance Variables (Uniform)         0
## 9      %. of Variables having complete cases  100% (9)
## 10 %. of Variables having <50% missing cases    0% (0)
## 11 %. of Variables having >50% missing cases    0% (0)
## 12 %. of Variables having >90% missing cases    0% (0)
```




```r
(
descricao2<-ExpData(rcl_dataset,2) %>% 
  select(-1) %>% 
  setNames(c("Nome da variável","Tipo de variável","% dados faltantes","No. valores únicos"))
)
```

```
##     Nome da variável Tipo de variável % dados faltantes No. valores únicos
## 1           decisao*           factor                 0                  2
## 2             agravo          logical                 0                  2
## 3    orgao_julgador*           factor                 0                 15
## 4          novo_cpc*           factor                 0                  2
## 5         instancia*           factor                 0                  3
## 6          segmento*           factor                 0                  5
## 7         paradigma*           factor                 0                  4
## 8 efeito_vinculante*           factor                 0                  2
## 9      tipo_decisao*           factor                 0                 13
```



```r
(
frequencias <- ExpCTable(rcl_dataset,Target="decisao",margin=1,clim=10,nlim=NULL,round=2,bin=NULL,per=T) %>% 
  setNames(c("Variável","Categoria","Número","decisao:improcedente","decisao:procedente","TOTAL"))
)
```

```
##             Variável            Categoria Número decisao:improcedente
## 1           novo_cpc             CPC/1973     nn              1961.00
## 2           novo_cpc             CPC/2015     nn              1711.00
## 3           novo_cpc                TOTAL     nn              3672.00
## 4           novo_cpc             CPC/1973      %                53.40
## 5           novo_cpc             CPC/2015      %                46.60
## 6           novo_cpc                TOTAL      %               100.00
## 7          instancia             primeira     nn              1080.00
## 8          instancia              segunda     nn              1905.00
## 9          instancia             superior     nn               687.00
## 10         instancia                TOTAL     nn              3672.00
## 11         instancia             primeira      %                29.41
## 12         instancia              segunda      %                51.88
## 13         instancia             superior      %                18.71
## 14         instancia                TOTAL      %               100.00
## 15          segmento            eleitoral     nn                32.00
## 16          segmento             estadual     nn              1371.00
## 17          segmento              federal     nn               374.00
## 18          segmento                  stj     nn               151.00
## 19          segmento             trabalho     nn              1744.00
## 20          segmento                TOTAL     nn              3672.00
## 21          segmento            eleitoral      %                 0.87
## 22          segmento             estadual      %                37.34
## 23          segmento              federal      %                10.19
## 24          segmento                  stj      %                 4.11
## 25          segmento             trabalho      %                47.49
## 26          segmento                TOTAL      %               100.00
## 27         paradigma controle_concentrado     nn              1250.00
## 28         paradigma         inter_partes     nn              1630.00
## 29         paradigma                 rext     nn               286.00
## 30         paradigma               sumula     nn               506.00
## 31         paradigma                TOTAL     nn              3672.00
## 32         paradigma controle_concentrado      %                34.04
## 33         paradigma         inter_partes      %                44.39
## 34         paradigma                 rext      %                 7.79
## 35         paradigma               sumula      %                13.78
## 36         paradigma                TOTAL      %               100.00
## 37 efeito_vinculante       nao_vinculante     nn              1630.00
## 38 efeito_vinculante           vinculante     nn              2042.00
## 39 efeito_vinculante                TOTAL     nn              3672.00
## 40 efeito_vinculante       nao_vinculante      %                44.39
## 41 efeito_vinculante           vinculante      %                55.61
## 42 efeito_vinculante                TOTAL      %               100.00
##    decisao:procedente   TOTAL
## 1             1818.00 3779.00
## 2              863.00 2574.00
## 3             2681.00 6353.00
## 4               67.81   59.48
## 5               32.19   40.52
## 6              100.00  100.00
## 7              637.00 1717.00
## 8             1497.00 3402.00
## 9              547.00 1234.00
## 10            2681.00 6353.00
## 11              23.76   27.03
## 12              55.84   53.55
## 13              20.40   19.42
## 14             100.00  100.00
## 15               3.00   35.00
## 16             552.00 1923.00
## 17             223.00  597.00
## 18              40.00  191.00
## 19            1863.00 3607.00
## 20            2681.00 6353.00
## 21               0.11    0.55
## 22              20.59   30.27
## 23               8.32    9.40
## 24               1.49    3.01
## 25              69.49   56.78
## 26             100.00  100.00
## 27             738.00 1988.00
## 28            1440.00 3070.00
## 29             218.00  504.00
## 30             285.00  791.00
## 31            2681.00 6353.00
## 32              27.53   31.29
## 33              53.71   48.32
## 34               8.13    7.93
## 35              10.63   12.45
## 36             100.00  100.00
## 37            1440.00 3070.00
## 38            1241.00 3283.00
## 39            2681.00 6353.00
## 40              53.71   48.32
## 41              46.29   51.68
## 42             100.00  100.00
```


## Valor da informação e peso de evidência

Em análise de respostas binárias, duas medidas muito utilizadas nas análises para concessão de crédito, mas quase desconhecidas nas demais áreas, são o peso da evidência e o valor da informação (WOE e IV nas siglas em inglês). Essas duas medidas são importantes na fase de exploração dos dados porque elas: 

1. Levam em conta a contribuição independente de cada variável para o resultado.
2. Detetam relações lineares e não lineares com a veriável resposta
3. Classificam as variáveis em termos de força preditiva “univariada”.
4. Visualize as correlações entre as variáveis preditivas e o resultado binário.
5. Comparam perfeitamente a força de variáveis contínuas e categóricas sem criar variáveis fictícias.
6. Tratam  perfeitamente de dados faltantes (missing) sem imputação.
7. Avaliam o poder preditivo dos dados faltantes.


WOE e IV são conceitos relacionados e foram gestados na teoria da informação a fim de medir o grau de incerteza envolvido na predição de eventos, dados os diferentes graus de conhecimento sobre as variáveis envolvidas. Em poucas palavras WOE descreve a relação entre uma variável preditiva e a variável binária alvo, no caso a decisão judicial, Por sua vez IV mede a força dessa relação.

WOE describes the relationship between a predictive variable and a binary target variable.

A tabela a seguir mostra os resultados

•	Variável – nome da variável

•	Decisão - Variável resposta (decisão judicial)

•	classe – classe da variável

•	out0 – Número de procedentes

•	out1 – Número de improcedentes

•	Total – Total de respostas para cada categoria

•	pe_1 – procedentes / total de procedentes (em percentual)

•	pe_0 – improcedentes / total de improcedentes (em percentual)

•	odds – pe_1/pe_0

•	woe – Peso da evidência (Weight of Evidence), calculado com o logarítimo natural de odds.

•	iv – Valor da informação (Information Value) - woe * (pe_0 – pe_1)



```
##             Variable  Target                Class Out_1 Out_0 TOTAL Per_1
## 1           novo_cpc decisao             CPC/1973  1818  1961  3779 0.678
## 2           novo_cpc decisao             CPC/2015   863  1711  2574 0.322
## 3          instancia decisao             primeira   637  1080  1717 0.238
## 4          instancia decisao              segunda  1497  1905  3402 0.558
## 5          instancia decisao             superior   547   687  1234 0.204
## 6           segmento decisao            eleitoral     3    32    35 0.001
## 7           segmento decisao             estadual   552  1371  1923 0.206
## 8           segmento decisao              federal   223   374   597 0.083
## 9           segmento decisao                  stj    40   151   191 0.015
## 10          segmento decisao             trabalho  1863  1744  3607 0.695
## 11         paradigma decisao controle_concentrado   738  1250  1988 0.275
## 12         paradigma decisao         inter_partes  1440  1630  3070 0.537
## 13         paradigma decisao                 rext   218   286   504 0.081
## 14         paradigma decisao               sumula   285   506   791 0.106
## 15 efeito_vinculante decisao       nao_vinculante  1440  1630  3070 0.537
## 16 efeito_vinculante decisao           vinculante  1241  2042  3283 0.463
##    Per_0  Odds    WOE    IV      Ref_1        Ref_0
## 1  0.534 1.270  0.239 0.034 procedente improcedente
## 2  0.466 0.691 -0.370 0.053 procedente improcedente
## 3  0.294 0.810 -0.211 0.012 procedente improcedente
## 4  0.519 1.075  0.072 0.003 procedente improcedente
## 5  0.187 1.091  0.087 0.001 procedente improcedente
## 6  0.009 0.111 -2.198 0.018 procedente improcedente
## 7  0.373 0.552 -0.594 0.099 procedente improcedente
## 8  0.102 0.814 -0.206 0.004 procedente improcedente
## 9  0.041 0.366 -1.005 0.026 procedente improcedente
## 10 0.475 1.463  0.380 0.084 procedente improcedente
## 11 0.340 0.809 -0.212 0.014 procedente improcedente
## 12 0.444 1.209  0.190 0.018 procedente improcedente
## 13 0.078 1.038  0.037 0.000 procedente improcedente
## 14 0.138 0.768 -0.264 0.008 procedente improcedente
## 15 0.444 1.209  0.190 0.018 procedente improcedente
## 16 0.556 0.833 -0.183 0.017 procedente improcedente
```


Para facilitar a interpretação dos resultados, tome-se em consideração os seguintes critérios:

Se o IV é  menor que 0.03 então o poder preditivo é = "Não preditivo"

Se o IV está de 0.3 para  0.1 então o poder preditivo é = "Moderadamente preditivo"

Se o IV está de  0.1 para 0.3 então o poder preditivo é = "Medianamente preditivo"

Se o IV é maior que  > 0.3 então o poder preditivo é = "Altamente preditivo"



```r
(
stat<-ExpCatStat(rcl_dataset,Target="decisao",Label="Decisões",result = "Stat",clim=15,Pclass="procedente")
)
```

```
##            Variable  Target Unique Chi-squared p-value df IV Value
## 1    orgao_julgador decisao     15     2812.44       0 14    2.818
## 2          novo_cpc decisao      2     132.848       0  1    0.087
## 3         instancia decisao      3      25.138       0  2    0.016
## 4          segmento decisao      5     332.982       0  4    0.231
## 5         paradigma decisao      4      61.453       0  3     0.04
## 6 efeito_vinculante decisao      2      53.544       0  1    0.035
## 7      tipo_decisao decisao     13    2811.992       0 12    2.798
##   Cramers V Degree of Association  Predictive Power
## 1      0.67                Strong Highly Predictive
## 2      0.14                  Weak    Not Predictive
## 3      0.06             Very Weak    Not Predictive
## 4      0.23              Moderate Medium Predictive
## 5       0.1                  Weak    Not Predictive
## 6      0.09                  Weak    Not Predictive
## 7      0.67                Strong Highly Predictive
```



## Fluxo dos processo

O gráfico abaixo mostra o fluxo dos processos tomando em conta cada uma das variáveis. Cada linha, roxa ou laranja, representa uma reclamação. A cor laranja representa o conjunto dos pedidos procedentes, a cor roxa representa o conjunto dos pedidos improcedentes. As variáveis foram ordenadas conforme a frequência com que decidem favoravelmente ou desfavoravelmete. 

Da visualização, é possível observar que as decisões colegiadas, i.e. decisões dos agravos, são maiormente improcedentes. Com efeito, do total, 1884 decisões colegiadas foram improcedentes e apenas 51 foram procedentes. Uma vez que essas decisão são regularmente proferidas ante de uma irresignação contra uma decisão monocrática, é possível afirmar com tranquilidade nesse caso, e não precisaríamos de qualquer teste estatístico para concluir isso, que as decisões colegiadas assumem papel nitidamente homologatório das decisões monocráticas.

Por outro lado, os ministros Alexandre de Moraes, Gilmar Mendes e Marco Aurélio lideram entre aqueles que mais concedem pedidos. Houve mais concessões após o CPC 2015 e a justiça federal e a justiça do trabalho são aparentemente as mais recalcitrantes. Por sua vez, as reclamações contra supostas violações de súmulas não são tão bem sucedidas quanto as reclamações contra supostas violações de recursos extraordinários e decisões interpartes.



```r
knitr::include_graphics("https://apps.consudata.com.br/shiny/gg_alluvial.png")
```

![](https://apps.consudata.com.br/shiny/gg_alluvial.png)<!-- -->

A tabela abaixo mostra as diferentes entre os órgãos julgadores em números


```
## # A tibble: 26 x 3
##    orgao_julgador      decisao          n
##    <fct>               <fct>        <int>
##  1 Alexandre de Moraes procedente     222
##  2 Alexandre de Moraes improcedente    91
##  3 Cármen Lúcia        procedente     281
##  4 Cármen Lúcia        improcedente   126
##  5 Celso de Mello      improcedente   188
##  6 Celso de Mello      procedente      28
##  7 Colegiada           improcedente  1884
##  8 Colegiada           procedente      51
##  9 Dias Toffoli        procedente     345
## 10 Dias Toffoli        improcedente   141
## # … with 16 more rows
```


## Referências







