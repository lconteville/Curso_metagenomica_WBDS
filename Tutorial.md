<h3> Máquina Virtual </h3>

Para realizar a instalação da Máquina Virtual, siga o passo-a-passo descrito no [README.md](./README.md). Toda a parte prática do curso poderá ser executada pela Máquina Virtual fornecida. No entanto, será possível acompanhar o curso mesmo se não for possível baixá-la ou instalá-la.

Alternativamente, alunxs com acesso a algum computador com o sistema operacional Linux instalado e conhecimento prévio em instalação de programas, podem baixar e instalar diretamente os programas que serão utilizados no workshop. Mais detalhes em: [Programas.md](./Programas.md)

<h3> Dados metagenômicos que serão utilizados </h3>

- **Microbioma Intestinal** de Indígena da Amazônia Brasileira ([Conteville et al, 2019](https://www.frontiersin.org/articles/10.3389/fmicb.2019.01743/full))
- **Microbioma de Solo** de Caatinga do Nordeste Brasileiro ([Lacerda-Júnior et al, 2019](https://www.frontiersin.org/articles/10.3389/fmicb.2019.00648/full))
- **Microbioma Aquático** de Praia no Uruguai ([Fresia et al, 2019](https://microbiomejournal.biomedcentral.com/articles/10.1186/s40168-019-0648-z))
- **Microbioma de Rúmen** de Búfalo Colombiano ([Aguilar-Marin et al, 2020](https://bmcmicrobiol.biomedcentral.com/articles/10.1186/s12866-020-02037-6#Sec8))

<h3> Download dos Metagenomas (já foi executado)</h3>

- Para conseguir baixar e analisar esses metagenomas, primeiramente precisamos de seu número de acesso (ex: SRR11041080) ou do número de acesso do projeto que ele pertence (ex: BioProject PRJNA605425). Normalmente esses números são apresentados no artigo a qual eles estão associados.
- Se os metagenomas foram depositados no NCBI, eles ficam armazenados no [Sequence Read Archive (SRA)](https://www.ncbi.nlm.nih.gov/sra), um repositório público para dados de sequenciamento de DNA.
- Para baixar algum dado do SRA, podemos usar o [SRA toolkit](https://ncbi.github.io/sra-tools/), que é uma coleção de ferramentas e bibliotecas para este fim. Nesta coleção existe a ferramenta <code>fasterq-dump</code>, que foi utilizada para baixar os metagenomas analisados neste workshop.
- Com o SRA toolkit instalado, só precisamos chamar a ferramenta fasterq-dump seguida do número de acesso do metagenoma de interesse:
    
    <code>fasterq-dump SRR11041080</code>
    
- O comando acima gera dois arquivos <code>.fastq</code>, que referem-se às sequências pareadas do metagenoma SRR11041080.

- Para facilitar, o nome desses arquivos foi mudado com o comando abaixo:

    <code>mv SRR11041080_1.fastq rumen_1.fastq</code>
    
    <code>mv SRR11041080_2.fastq rumen_2.fastq</code>

<h3> Arquivos Fastq </h3>

- Vamos dar uma olhada nesses arquivos Fastq? Vamos começar acessando a pasta que estão nossos dados com o comando <code>cd</code>:

    <code> cd Desktop/Dados/Dados_Brutos</code>

- Para ler um arquivo pelo terminal podemos usar o comando <code>head</code>. Esse comando mostra no terminal o conteúdo das 10 primeiras linhas de um arquivo.

    <code>head rumen_1.fastq</code>

- Os Fastq são arquivos de texto em que as informações sobre cada sequência estão dispostas em 4 linhas:
    * 1ª linha: O nome da sequência.
    * 2ª linha: A sequência nucleotídica (com bases A, T, C, G e N).
    * 3ª linha: Um  sinal de mais (+), que é simplesmente um separador e pode vir seguido do nome da sequência.
    * 4ª linha: As pontuações de qualidade de cada base da sequência.

<h3> Análise de Qualidade </h3>

- Para analisar a qualidade das sequências do Fastq, vamos utilizar o programa [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/), que fornece uma maneira simples de verificar a qualidade de sequências provenientes de sequenciamento de alto rendimento.

- Com o FastQC instalado, só precisamos chamá-lo seguido de um arquivo fastq:

    <code>fastqc  rumen_1.fastq</code>
    
    <code>fastqc  rumen_2.fastq</code>

- Cada um dos comandos acimam gera um arquivo <code>.html</code> e um arquivo <code>.zip</code>. O arquivo <code>.html</code> contêm um relatório final com todas as análises de qualidade realizadas. O arquivo <code>.zip</code> é um arquivo compactado das figuras e textos que compõem o <code>.html</code>. Para abrir um arquivo <code>.html</code> pelo terminal podemos usar a ferramenta [xdg-open](https://linux.die.net/man/1/xdg-open), que normalmente já vem instalada, seguida do nome do arquivo:

    <code>xdg-open rumen_1_fastqc.html</code>
    
    <code>xdg-open rumen_2_fastqc.html</code>

- Pelos gráficos gerados pelo FastQC podemos ter uma ideia geral de como estão as nossas sequências. No site do FastQC são fornecidos exemplos de arquivos html de um metagenoma com [alta qualidade](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/good_sequence_short_fastqc.html) e [baixa qualidade](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/bad_sequence_fastqc.html).

<h3> Trimagem e Filtragem </h3>

- A trimagem (corte) de adaptadores e bases de baixa qualidade é uma etapa importante na análise de dados de sequenciamento. Essas regiões são indesejadas pois podem causar erros e viéses nas análises posteriores.

- Para esta etapa, utilizaremos o programa [cutadapt](https://cutadapt.readthedocs.io/en/stable/). Este programa fornece diversos parâmetros que podem ser utilizados para melhorar a qualidade das nossas sequências. Execute o comando abaixo para ver todas as opções de parâmetros:

    <code>cutadapt --help</code>
        
- Quando se sabe quais adaptadores foram utilizados no sequenciamento dos metagenomas, usamos os parâmetros <code>-a</code>, <code>-g</code>, <code>-b</code> seguidos das sequências dos adaptadores. Dessa forma, esses adaptadores serão removidos das sequências (ou da primeira leitura em um par se os dados forem paired-end). Se especificado várias vezes, apenas o melhor adaptador correspondente será cortado. Um arquivo FASTA com as sequências dos adaptadores também pode ser fornecido ao programa. 

- Como aqui estamos analisando dados de diversos sequenciamentos, vamos cortar direto as bases que parecem "enviesadas" no começo e final das sequências. Para isso podemos usar o parâmetro <code>-u</code> (ou <code>--cut</code>). Neste parâmetro, se o número fornecido for positivo, as bases são removidas do início, e se for negativo, são removidas do final. O parâmetro <code>-o</code> (ou <code>--output</code>) deve ser usado para informar o nome do arquivo de saída. Dessa forma, o comando final fica assim:

    <code>cutadapt -u 20 -u -10 -o rumen_1.cut.fastq rumen_1.fastq</code>
    
    <code>cutadapt -u 20 -u -10 -o rumen_2.cut.fastq rumen_2.fastq</code>

- Será que a qualidade melhorou? Vamos rodar o [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) novamente para checar:

    <code>fastqc rumen_1.cut.fastq rumen_2.cut.fastq</code>
    
- Em seguida, vamos visualizar os <code>.html</code> gerados:

    <code>xdg-open rumen_1.cut_fastqc.html </code>
    
    <code>xdg-open rumen_2.cut_fastqc.html </code>

- Dá pra melhorar ainda mais né? Então vamos rodar o cutadapt novamente nesses dados. Agora podemos lidar com os dois arquivos ao mesmo tempo.  O parâmetro <code>-q</code> (ou <code>--quality-cutoff</code>) será usado para cortar as bases de baixa qualidade das leituras. E o parâmetro <code>-m</code> (ou <code>--minimum-length</code>) será usado para descartar as sequências curtas demais. Em metagenomas paired-end, devemos fornecer dois arquivos de entrada, e dois arquivos de saída com os parâmetros <code>-o</code> (ou <code>--output</code>) e <code>-p</code> (ou <code>--pair-output</code>). Também usaremos o parâmetro <code>--pair-filter = any</code>, o que significa que se uma sequência que atender ao critério de filtragem, o seu também será descartado. Então o comando fica assim:

    <code>cutadapt -q 20 -m 30 -o rumen_1.q20.fastq -p rumen_2.q20.fastq rumen_1.cut.fastq rumen_2.cut.fastq --pair-filter=any</code>

- Para saber se essa etapa funcionou, podemos chamar novamente o programa [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) seguido do output do comando acima:

    <code>fastqc rumen_*.q20.fastq</code>
    
- Em seguida, vamos visualizar os <code>.html</code> gerados:

    <code>xdg-open rumen_1.q20_fastqc.html </code>
    
    <code>xdg-open rumen_2.q20_fastqc.html </code>
    
- Se estamos satisfeitxs com o resultado do fastqc, podemos seguir para as próximas análises.
   
<h3> Análise Taxonômica </h3>

- Primeiro vamos sair da pasta Dados_Brutos e acessar a pasta que estão nossos outros dados com o comando <code>cd</code>:

    <code> cd ../Analise_Taxonomica</code>

- Nesta etapa identificaremos quais organismos estão nas nossas amostras com o programa [MetaPhlAn](http://huttenhower.sph.harvard.edu/metaphlan) (Metagenomic Phylogenetic Analysis). A classificação feita pelo [MetaPhlAn](http://huttenhower.sph.harvard.edu/metaphlan) baseia-se em seu banco de dados de genes marcadores únicos identificados em genomas de referência de bactérias, arquéias, vírus e eucariotos. Execute o comando abaixo para ver todos os parâmetros que podem ser usados no metaphlan.

    <code>metaphlan -h</code>
    
- Como nossos arquivos são <code>.fastq</code>, devemos avisar isso ao programa com o parâmetro <code>--input_type</code>. Os dois arquivos que serão usados como input nesse programa devem estar separados por uma vírgula. Utilizaremos o sinal <code>></code> para mandar o resultado do comando para o arquivo <code>caatinga_metaphlan.txt</code>. O comando final ficaria como demonstrado abaixo, mas <b>NÃO vamos rodar esse comando no workshop</b>. Se o comando abaixo for executado, irá baixar a base de dados do metaphlan automaticamente e depois realizar a análise taxonômica dos <code>.fastq</code>. Se o comando for executado por acidente, ele pode ser interrompido com <code>Ctrl+C</code>.

    <code>metaphlan --input_type fastq caatinga_1.q20.fastq,caatinga_2.q20.fastq --bowtie2out caatinga_bowtie2out.txt > caatinga_metaphlan.txt</code>
    
- O comando acima gera 2 arquivos de texto. Para dar uma olhada nos resultados, vamos para o diretório <code>output_caatinga</code>, onde deixei pronto os resultados. 

    <code> cd output_caatinga </code>
  
- Para visualizar os arquivos de saída podemos utilizar o comando <code>less</code> seguido do nome do arquivo. Esse comando irá exibir o conteúdo do arquivo no terminal e você pode usar as teclas para cima e para baixo no teclado e fazer a paginação. Para sair do <code>less</code> pressione a tecla <code>q</code> em seu teclado.
 
    <code>less caatinga_bowtie2out.txt</code>
 
    <code>less caatinga_metaphlan.txt</code>
    
- O arquivo <code>caatinga_bowtie2out.txt</code> contém resultados dos mapeamentos (executado pelo bowtie2, que é chamado pelo metaphlan) listados um por linha em colunas separadas por nome da sequência metagenômica e a referência para qual esta sequência mapeou. 

- O arquivo <code>caatinga_metaphlan.txt</code> possui um cabeçalho de 4 linhas iniciadas por <code>#</code>. A primeira linha lista o banco de dados de genes marcadores que o MetaPhlAn usa. A segunda linha lista o comando que foi utilizado para gerá-lo. A quarta linha contém os cabeçalhos das colunas que estão abaixo. Mais abaixo, temos uma tabela onde:
    - A primeira coluna lista os clados, de reinos (Bactérias, Archaea, etc.) a espécies:
    Reino: k__, Filo: p__, Classe: c__, Ordem: o__, Família: f__, Gênero: g__, Espécie: s__
    - A segunda coluna lista os número de acesso desses táxons no NCBI. A terceira coluna lista as abundâncias relativas por nível, ou seja cada nível somará 100%. A quarta coluna lista as espécies adicionais para os casos em que o perfil do metagenoma contém clados que representam várias espécies. As espécies listadas na coluna 1 são as espécies representativas.

- Podemos filtrar esse resultado e olhar só as famílias que foram identificadas utilizando o comando <code>grep</code>:
 
    <code>grep f__ caatinga_metaphlan.txt | grep -v g__</code>
 
- Para gerar a primeira figura, precisaremos de um arquivo que tenha os resultados de todos os metagenomas. Vamos primeiro copiar os resultados dos outros metagenomas para o diretório em que estamos usando o comando <code>cp</code>:

    <code>cp ../outputs/*_metaphlan.txt .</code>

- Para gerar um arquivo com o resultado de várias análises taxonômicas, podemos utilizar um script do metaphlan chamado <code>merge_metaphlan_tables.py</code>. Para utilizá-lo, só precisamos chamá-lo seguido dos arquivos que queremos juntar:

    <code>merge_metaphlan_tables.py rumen_metaphlan.txt caatinga_metaphlan.txt yanomami_metaphlan.txt praia_metaphlan.txt > merged_metaphlan.txt</code>
    
- Esse arquivo tem informações de reinos a espécies, vamos filtrar para ficar só com os filos:

    <code>head -1 merged_metaphlan.txt > merged_metaphlan_filos.txt ; grep -E "clade|p__" merged_metaphlan.txt | grep -v "c__" | sed 's/^.*p__//g' >> merged_metaphlan_filos.txt </code>
    
- Agora vamos criar um heatmap com <code>hclust2</code> usando esse arquivo que acabamos de gerar: 

    <code>hclust2.py -i merged_metaphlan_filos.txt -o merged_metaphlan_filos.png --f_dist_f braycurtis --s_dist_f braycurtis --cell_aspect_ratio 0.5 -l --flabel_size 10 --slabel_size 10 --max_flabel_len 100 --max_slabel_len 100 --minv 0.1 --dpi 200</code>
    
- Podemos abrir o arquivo <code>.png</code> que foi gerado usando a linha de comando:

    <code>shotwell merged_metaphlan_filos.png</code>

<h3> Análise Funcional </h3>

- Vamos sair da pasta que estamos (<code>Analise_Taxonomica/output_caatinga</code>) e acessar a pasta que estão nossos dados para análise funcional com o comando <code>cd</code>:

    <code> cd ../../Analise_Funcional</code>

- Agora que sabemos quais táxons estão nas nossas amostras, vamos buscar quais funções esses táxons podem estar executando. O [SUPER-FOCUS](https://github.com/metageni/SUPER-FOCUS)(SUbsystems Profile by databasE Reduction using FOCUS) usa o banco de dados [SEED](https://www.theseed.org/wiki/Home_of_the_SEED) para caracterizar os subsistemas presentes nos metagenomas.

- Execute o comando abaixo para ver todas os parâmetros possíveis e necessários para rodar o [SUPER-FOCUS](https://github.com/metageni/SUPER-FOCUS).

    <code>superfocus -h</code>

- Utilizamos <code>-q</code> para fornecer o caminho do diretório em que estão os nossos arquivos fastq e <code>-dir</code> para fornecer o caminho do diretório em que serão salvos os arquivos de saída. O SUPER-FOCUS permite realizarmos o alinhamento com 3 alinhadores diferentes: DIAMOND, RAPSearch2 e BLAST. Com o parâmetro <code>-a</code> indicamos qual deles vamos usar. No [github](https://github.com/metageni/SUPER-FOCUS) do SUPER-FOCUS, os autores deixaram recomendações para o uso de cada alinhador. E por último, temos o parâmetro <code>-db</code> para informar a base de dados utilizada. Dessa forma, o comando final fica assim:
    
    <code>superfocus -q . -dir . -a diamond -db DB_90</code>
    
- O comando acima gerará sete arquivos, sendo cinco arquivos <code>.xls</code> com as abundâncias de cada subsistema/função identificada nos metagenomas e dois arquivos <code>.m8</code> com informações sobre as sequências e os alinhamentos realizados contra o banco de dados. 

- Devido ao tamanho da base de dados original e às limitações da Máquina Virtual, utilizamos aqui uma pequena parte da base de dados original. No diretório <code>output_yanomami_completo</code> podemos encontrar o resultado gerado com a base de dados original.

    <code>cd output_yanomami_completo</code>

- Utilize os comandos <code>head</code> ou <code>less</code> para analisar cada arquivo de saída. Para sair do <code>less</code> pressione a tecla <code>q</code> em seu teclado.

    <code>less output_subsystem_level_1.xls</code>

- Nos arquivos <code>.xls</code> podemos ver que as abundâncias são geradas separadamente para cada arquivo do par. Para facilitar as próximas análises, vamos somar as abundâncias e dividir por 2:

    <code>cut -d$'\t' -f1,4,5 output_subsystem_level_1.xls | awk -F$'\t' 'NR>=6 { print $1"\t"($2+$3)/2 }' > yanomami_level1.tsv</code>
    
- Para facilitar, já deixei os outputs dos outros metagenomas no diretório <code>outputs</code>. Vamos copiá-los para a pasta que estamos e trabalhar com eles:

    <code> cp ../outputs/*_level1.tsv . </code>
        
- Assim como fizemos na análise taxonômica, vamos criar um arquivo com os resultados da análise funcional de todos os metagenomas. Vamos usar o script do metaphlan chamado <code>merge_metaphlan_tables.py</code>. Para utilizá-lo só precisamos chamá-lo seguido dos arquivos que queremos juntar:

    <code>merge_metaphlan_tables.py *_level1.tsv > merged_level1.txt</code>
         
- Agora vamos criar um heatmap com <code>hclust2</code> usando esse arquivo que acabamos de gerar: 

    <code>hclust2.py -i merged_level1.txt -o merged_level1.png --f_dist_f braycurtis --s_dist_f braycurtis --cell_aspect_ratio 0.5 -l --flabel_size 10 --slabel_size 10 --max_flabel_len 100 --max_slabel_len 100 --minv 0.1 --dpi 300</code>
    
- Vamos abrir o arquivo <code>.png</code> que foi gerado usando a linha de comando:

    <code>shotwell merged_level1.png</code>

<h3> Análises Estatísticas</h3>

- No diretório <code>Analises_Estatisticas</code> estão os arquivos com os resultados das análises taxonômica e funcional de todos os metagenomas:

    <code>cd ../../Analises_Estatisticas</code>

- Para esta etapa vamos usar a linguagem [R](https://www.r-project.org/) e alguns pacotes, como o [phyloseq](https://joey711.github.io/phyloseq/). Vamos iniciar chamando o R e os pacotes que serão utilizados:

    <code>R</code>
    
    <code>library("phyloseq")</code>
    
    <code>library("ggplot2")</code>

    <code>library("plyr")</code>

    <code>library("ggpubr")</code>
    

<h4>Análise Taxonômica</h4>
    
- Vamos importar o arquivo com os resultados do metaphlan

    <code>tabela <- read.csv("merged_metaphlan.tsv", sep="\t",row.names = 1, header=TRUE)</code>
    
- Filtrar para ficar apenas com as abundâncias dos gêneros identificados
    
    <code>rows_genero <- setdiff(grep("g__",rownames(tabela)),grep("s__",rownames(tabela)))</code>

    <code>tabela_genero <- tabela[rows_genero,]</code>
    
    <code>abundance <- otu_table(tabela_genero, taxa_are_rows = TRUE)</code>
        
- Gerar uma matrix com as informações taxonômicas
        
    <code>nomes_generos = rownames(tabela_genero)</code>
        
    <code>lista = strsplit(nomes_generos, split="|", fixed=TRUE)</code>
        
    <code>taxonomia <- matrix(unlist(lista), nrow=length(lista), byrow=TRUE)</code>
        
    <code>colnames(taxonomia) = c("Reino", "Filo", "Classe", "Ordem", "Familia", "Genero")[1:ncol(taxonomia)]</code>
        
    <code>rownames(taxonomia) = rownames(abundance)</code>
        
    <code>TAX = tax_table(taxonomia)</code>

- Gerar um data frame com os metadados sobre os metagenomas
        
    <code>Group <- c("Caatinga","Praia","Rumen","Yanomami")</code>
                   
    <code>Data <- rep("Metagenomas",4)</code>
                  
    <code>tabela_amostras <- data.frame(Group,Data)</code>
                             
    <code>rownames(tabela_amostras) <- colnames(abundance)</code>
                                       
    <code>metadata = sample_data(tabela_amostras)</code>

- Criar um objeto phyloseq
        
    <code>physeq = phyloseq(abundance, TAX, metadata)</code>
        
    <code>physeq</code>

- Beta-diversidade PcoA com Bray-Curtis. [Exemplos de output com mais amostras](https://joey711.github.io/phyloseq/plot_ordination-examples.html)
        
    <code>ord <- ordinate(physeq, "PCoA", "bray")</code>
        
    <code>plot_ordination(physeq, ord, color = "Group", axes=c(2,3)) + geom_point(size = 3)</code>

- Também podemos fazer análise de alfa-diversidade, mas para isso, não podemos ter os dados em Abundância Relativa, como é gerado pelo Metaphlan. [Exemplos de output](https://joey711.github.io/phyloseq/plot_richness-examples.html)
        
    <code>plot_richness(physeq)</code>

- BarPlot por gêneros
        
    <code>plot_bar(physeq, x="Group", fill="Genero")</code>

- BarPlot por filos
        
    <code>physeq_filo = tax_glom(physeq, "Filo")</code>
        
    <code>plot_bar(physeq_filo, x="Group", fill="Filo")</code>

    <code>dat <- psmelt(physeq_filo)</code>
        
    <code>medians <- ddply(dat, ~Filo, function(x) c(median=median(x$Abundance)))</code>
        
    <code>remover <- medians[medians$median <= 0.1,]$Filo</code>
        
    <code>dat$Filo <- as.character(dat$Filo)</code>
        
    <code>dat[dat$Filo %in% remover,]$Filo <- "Outros"</code>
        
    <code>ggplot(dat, aes(fill=Filo, y=Abundance, x=Group)) + 
            geom_bar(position="stack", stat="identity")+
            theme_bw()</code>

- Se tivéssemos mais amostras de cada microbioma, poderíamos fazer um BoxPlot
        
    <code>dat2 <- dat[!(dat$Filo == "Outros"), ]</code>

    <code>ggplot(dat2, aes(x=Group, y=Abundance, fill=Filo)) + geom_boxplot()</code>

- Poderíamos analisar se a abundância de algum filo é significativamente diferente

    <code>dat3 <- dat[(dat$Filo %in% "p__Bacteroidetes"), ]</code>

    <code>my_comparisons <- list( c("Praia", "Caatinga"), c("Caatinga", "Rumen"), c("Praia", "Yanomami") )</code>

    <code>ggplot(dat3, aes(y=Abundance, x=Group, fill=Filo)) + 
            geom_bar(stat = "identity")+
            stat_compare_means(method = "wilcox.test", comparisons = my_comparisons, size = 3.5)</code>

- Heatmap

    <code>plot_heatmap(physeq, "NMDS", "bray", "Group", "Genero", low="#000033", high="#CCFF66")</code>
        
        
<h4>Análise Funcional</h4>

- Vamos importar o arquivo com os resultados do superfocus

    <code>tabela2 <- read.csv("merged_level1.tsv", sep="\t",row.names = 1, header=TRUE)</code>
        
    <code>abundance2 <- otu_table(tabela2, taxa_are_rows = TRUE)</code> 

- Gerar uma matrix com as informações taxonômicas
    
    <code>nomes_level1 = rownames(abundance2)</code> 
        
    <code>taxonomia2 <- matrix(nomes_level1)</code> 

    <code>colnames(taxonomia2) <- "Level1"</code> 
                                  
    <code>rownames(taxonomia2) <- nomes_level1</code> 
        
    <code>TAX2 = tax_table(taxonomia2)</code> 

- Gerar um data frame com os metadados sobre os metagenomas

    <code>tabela_amostras2 <- data.frame(Group,Data)</code> 
                              
    <code>rownames(tabela_amostras2) <- colnames(abundance2)</code> 

    <code>metadata2 <- sample_data(tabela_amostras2)</code> 

- Criar um objeto phyloseq
                       
    <code>physeq2 <- phyloseq(abundance2, TAX2, metadata2)</code> 

    <code>physeq2</code> 

- Beta-diversidade PcoA com Bray-Curtis
        
    <code>ord2 <- ordinate(physeq2, "PCoA", "bray")</code> 
                  
    <code>plot_ordination(physeq2, ord2, color = "Group") + geom_point(size = 3)</code> 

- BarPlot 
        
    <code>plot_bar(physeq2, x="Group", fill="Level1")</code> 

    <code>dat4 <- psmelt(physeq2)</code> 
                  
    <code>medians2 <- ddply(dat4, ~Level1, function(x) c(median=median(x$Abundance)))</code> 
                      
    <code>remover2 <- medians2[medians2$median <= 6,]$Level1</code> 
    
    <code>dat4$Level1 <- as.character(dat4$Level1)</code>
        
    <code>dat4[dat4$Level1 %in% remover2,]$Level1 <- "Outros"</code> 
                                                         
    <code>ggplot(dat4, aes(fill=Level1, y=Abundance, x=Group)) + geom_bar(position="stack", stat="identity") + theme_bw()</code> 

- Poderíamos analisar se a abundância de algum filo é significativamente diferente

    <code>dat5 <- dat4[(dat4$Level1 %in% "Carbohydrates"), ]</code> 

    <code>my_comparisons <- list( c("Praia", "Caatinga"), c("Caatinga", "Rumen"), c("Praia", "Yanomami"))</code> 

    <code>ggplot(dat5, aes(y=Abundance, x=Group, fill=Level1)) + geom_bar(stat = "identity") + stat_compare_means(method = "wilcox.test", comparisons = my_comparisons, size = 3.5)</code>

- Heatmap

    <code>plot_heatmap(physeq2, "MDS", "bray", "Group", "Level1", low="#000033", high="#CCFF66")</code>     

- Para sair do ambiente R:
   
    <code>quit()</code>

    

