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

- Vamos dar uma olhada nesses arquivos Fastq? Para ler um arquivo pelo terminal, podemos usar o comando <code>head</code>. Esse comando mostra no terminal o conteúdo das 10 primeiras linhas de um arquivo.

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

- ~Cada um dos comandos acimam gera um arquivo <code>.html</code> e um arquivo <code>.zip</code>. O arquivo <code>.html</code> contêm um relatório final com todas as análises de qualidade realizadas. O arquivo <code>.zip</code> é um arquivo compactado das figuras e textos que são resultados das análises. As imagens no arquivo <code>.zip</code> são as mesmas que formam o arquivo <code>.html</code>. Para abrir um arquivo <code>.html</code> pelo terminal podemos usar a ferramenta [xdg-open](https://linux.die.net/man/1/xdg-open), que normalmente já vem instalada, seguida do nome do arquivo:

    <code>xdg-open rumen_1_fastqc.html </code>
    
    <code>xdg-open rumen_2_fastqc.html </code>

- Pelos gráficos gerados pelo FastQC podemos ter uma ideia geral de como estão as nossas sequências. No site do FastQC são fornecidos exemplos de arquivos html de um metagenoma com [alta qualidade](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/good_sequence_short_fastqc.html) e [baixa qualidade](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/bad_sequence_fastqc.html).

<h3> Trimagem e Filtragem </h3>

- No geral, os metagenomas analisados nesse workshop não precisam de uma limpeza por já terem sido trimados antes de serem adicionados na base de dados, mas vamos aumentar ainda mais a qualidade de um deles.

- A trimagem (corte) de adaptadores e bases de baixa qualidade é uma etapa importante na análise de dados de sequenciamento. Essas regiões são indesejadas por causarem erros ou viéses nas análises.

- Para esta etapa, utilizaremos o programa [cutadapt](https://cutadapt.readthedocs.io/en/stable/). Este programa fornece diversos parâmetros que podem ser utilizados para melhorar a qualidade das nossas sequências. Execute o comando abaixo para ver todas as opções de parâmetros:

    <code>cutadapt --help</code>
        
- Quando se sabe quais adaptadores foram utilizados no sequenciamento dos metagenomas, usamos os parâmetros <code>-a</code>, <code>-g</code>, <code>-b</code> seguidos das sequências dos adaptadores. Dessa forma, esses adaptadores serão removidos das sequências (ou da primeira leitura em um par se os dados forem paired-end). Se especificado várias vezes, apenas o melhor adaptador correspondente será cortado. Um arquivo FASTA com as sequências dos adaptadores também pode ser fornecido ao programa. 

- Como aqui estamos analisando dados de diversos sequenciamentos, vamos cortar direto as bases que parecem "enviesadas" no começo e final das sequências. Para isso podemos usar o parâmetro <code>-u</code> (ou <code>--cut</code>). Neste parâmetro, se o número fornecido for positivo, as bases são removidas do início, e se for negativo, são removidas do final. O parâmetro <code>-o</code> deve ser usado para informar o nome do arquivo de saída. Dessa forma, o comando final fica assim:

    <code>cutadapt -u 20 -u -10 -o rumen_1.cut.fastq rumen_1.fastq</code>
    
    <code>cutadapt -u 20 -u -10 -o rumen_2.cut.fastq rumen_2.fastq</code>

- Será que a qualidade melhorou? Vamos rodar o [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) novamente para checar:

    <code>fastqc rumen_1.cut.fastq rumen_2.cut.fastq</code>
    
- Quando rodar podemos visualizar os <code>.html</code> gerados:

    <code>xdg-open rumen_1.cut_fastqc.html </code>
    
    <code>xdg-open rumen_2.cut_fastqc.html </code>

- Dá pra melhorar ainda mais né? Então vamos rodar o cutadapt novamente nesses dados. Agora podemos lidar com os dois arquivos ao mesmo tempo.  O parâmetro <code>-q</code> (ou <code>--quality-cutoff</code>) será usado para cortar as bases de baixa qualidade das leituras. E o parâmetro <code>-m</code> (ou <code>--minimum-length</code>) será usado para descartar as sequências curtas demais. Em metagenomas paired-end, devemos fornecer dois arquivos de entrada e um segundo arquivo de saída com o parâmetro <code>-p</code> (ou <code>--pair-output</code>). Também usaremos o parâmetro <code>--pair-filter = any</code>, o que significa que o par de uma sequência será descartado se ela atender ao critério de filtragem. Então o comando fica assim:

    <code>cutadapt -q 20 -m 30 -o rumen_1.q20.fastq -p rumen_2.q20.fastq rumen_1.cut.fastq rumen_2.cut.fastq --pair-filter=any</code>

- Para saber se essa etapa funcionou, podemos chamar novamente o programa [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) seguido do output do comando acima:

    <code>fastqc rumen_1.q20.fastq rumen_2.q20.fastq</code>
    
 - Quando rodar podemos visualizar os <code>.html</code> gerados:

    <code>xdg-open rumen_1.q20_fastqc.html </code>
    
    <code>xdg-open rumen_2.q20_fastqc.html </code>
    
- Se estamos satisfeitxs com o resultado do fastqc, podemos seguir para as próximas análises.
   
<h3> Análise Taxonômica </h3>

- Nesta etapa identificaremos quais organismos estão nas nossas amostras com o programa [MetaPhlAn](http://huttenhower.sph.harvard.edu/metaphlan) (Metagenomic Phylogenetic Analysis). A classificação feita pelo [MetaPhlAn](http://huttenhower.sph.harvard.edu/metaphlan) baseia-se em seu banco de dados de genes marcadores únicos identificados em genomas de referência de bactérias, arquéias, vírus e eucariotos. Execute o comando abaixo para ver todos os parâmetros que podem ser usados no metaphlan.

    <code>metaphlan -h</code>
    
- Como nossos arquivos são <code>.fastq</code>, devemos avisar isso ao programa com o parâmetro <code>--input_type</code>. Os dois arquivos que serão usados como input nesse programa devem estar separados por uma vírgula. Utilizaremos o sinal <code>></code> para mandar o resultado do comando para o arquivo <code>caatinga_metaphlan.txt</code>. Dessa forma, o comando final fica assim:

    <code>metaphlan --input_type fastq caatinga/19_S19_L003_R1_001.q20.fastq,caatinga/19_S19_L003_R2_001.q20.fastq --bowtie2out caatinga/caatinga_bowtie2out.txt > caatinga/caatinga_metaphlan.txt</code>
    
- O comando acima gerará 2 arquivos de texto. Para dar uma olhada nos resultados, podemos utilizar o comando <code>less</code> seguido do nome do arquivo. O comando irá exibir o conteúdo do arquivo e você pode usar as teclas para cima e para baixo no teclado e fazer a paginação do arquivo.
 
    <code> less caatinga_bowtie2out.txt</code>
 
    <code> less caatinga_metaphlan.txt</code>
    
- O arquivo <code>caatinga_bowtie2out.txt</code> contém resultados do mapeamento feito pelo metaphlan listados um por linha em colunas separadas por nome da sequência metagenômica e a referência para qual esta sequência mapeou. 

- O arquivo <code>caatinga_metaphlan.txt</code> possui um cabeçalho de 4 linhas iniciadas por <code> # </code>. A primeira linha lista o banco de dados de genes marcadores que o MetaPhlAn usa. A segunda linha lista o comando que foi utilizado para gerá-lo. A quarta linha contém os cabeçalhos das colunas que estão abaixo.

- A primeira coluna lista os clados, de reinos (Bactérias, Archaea, etc.) a espécies:
    Reino: k__, Filo: p__, Classe: c__, Ordem: o__, Família: f__, Gênero: g__, Espécie: s__

- A segunda coluna lista os número de acesso desses táxons no NCBI. A terceira coluna lista as abundâncias relativas por nível, ou seja cada nível somará 100%. A quarta coluna lista as espécies adicionais para os casos em que o perfil do metagenoma contém clados que representam várias espécies. As espécies listadas na coluna 1 são as espécies representativas.

- Podemos filtrar esse resultado e olhar só as famílias que foram identificadas utilizando o comando <code>grep</code>:
 
    <code>grep f__ caatinga_metaphlan.txt | grep -v g__</code>
 
 - Para facilitar, o metaphlan já foi rodado nas outras amostras, para visualizar o resultado, podemos usar o comando <code>less</code> seguido do nome dos outros arquivos.

<h3> Análise Funcional </h3>

- Agora que sabemos quais táxons estão nas nossas amostras, vamos buscar que funções esse táxons podem estar executando. O [SUPER-FOCUS](https://github.com/metageni/SUPER-FOCUS)(SUbsystems Profile by databasE Reduction using FOCUS) usa o banco de dados [SEED](https://www.theseed.org/wiki/Home_of_the_SEED) para relatar os subsistemas presentes nos metagenomas e traçar o perfil de suas abundâncias.

- Execute o comando abaixo para ver todas os parâmetros possíveis e necessários para rodar o [SUPER-FOCUS](https://github.com/metageni/SUPER-FOCUS).

    <code>python3 superfocus.py -h</code>

- Devemos fornecer o caminho do diretório em que estão os nossos arquivos fastq (<code>-q</code>) e o caminho para o diretório em que serão salvos os arquivos de saída (<code>-dir</code>). O SUPER-FOCUS permite realizarmos o alinhamento com 3 alinhadores diferentes: DIAMOND, RAPSearch2 e BLAST. Com o parâmetro <code>-a</code> indicamos qual deles vamos usar. No [github](https://github.com/metageni/SUPER-FOCUS) do SUPER-FOCUS, os autores deixaram recomendações para o uso de cada alinhador. E por último, temos o parâmetro <code>-db</code> para informar a base de dados utilizada. Dessa forma, o comando final fica assim:
    
    <code>python3 superfocus.py -q yanomami/menor/ -dir yanomami/superfocus/ -a diamond -db DB_90</code>
    
- O comando acima gerará seis arquivos.


<h3> Análises Estatísticas</h3>
Shaman - https://shaman.pasteur.fr/
