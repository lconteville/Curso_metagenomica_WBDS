<h3> Dados metagenômicos que serão utilizados </h3>

- **Microbioma Intestinal** de Indígena da Amazônia Brasileira ([Conteville et al, 2019](https://www.frontiersin.org/articles/10.3389/fmicb.2019.01743/full))
- **Microbioma de Solo** de Caatinga do Nordeste Brasileiro ([Lacerda-Júnior et al, 2019](https://www.frontiersin.org/articles/10.3389/fmicb.2019.00648/full))
- **Microbioma Aquático** de Praia no Uruguai ([Fresia et al, 2019](https://microbiomejournal.biomedcentral.com/articles/10.1186/s40168-019-0648-z))
- **Microbioma de Rúmen** de Búfalo Colombiano ([Aguilar-Marin et al, 2020](https://bmcmicrobiol.biomedcentral.com/articles/10.1186/s12866-020-02037-6#Sec8))

<h3> Download dos Metagenomas </h3>

- Para conseguir baixar e analisar esses metagenomas, primeiramente precisamos de seu número de acesso (ex: SRR8732219) ou do número de acesso do projeto que ele pertence (ex: BioProject PRJNA527208). Normalmente esses números são apresentados no artigo a qual eles estão associados.
- Se os metagenomas foram depositados no NCBI, eles ficam armazenados no [Sequence Read Archive (SRA)](https://www.ncbi.nlm.nih.gov/sra), um repositório público para dados de sequenciamento de DNA.
- Para baixar algum dado do SRA, podemos usar o [SRA toolkit](https://ncbi.github.io/sra-tools/), que é uma coleção de ferramentas e bibliotecas para este fim. Nesta coleção existe a ferramenta <code>fasterq-dump</code>, que foi utilizada para baixar os metagenomas analisados neste workshop.
- Com o SRA toolkit instalado, só precisamos chamar a ferramenta fasterq-dump seguida do número de acesso do metagenoma de interesse:
    
    <code>fasterq-dump SRR8732219</code>
    
- O comando acima gera dois arquivos <code>.fastq</code>, que referem-se às sequências pareadas do metagenoma SRR8732219.

<h3> Arquivos Fastq </h3>

- Vamos dar uma olhada nesses arquivos Fastq? Para ler um arquivo pelo terminal, podemos usar o comando <code>head</code>. Esse comando mostra no terminal o conteúdo das 10 primeiras linhas de um arquivo.

    <code>head SRR8732219_1.fastq</code>

- Os Fastq são arquivos de texto em que as informações sobre cada sequência estão dispostas em 4 linhas:
    * 1ª linha: O nome da sequência.
    * 2ª linha: A sequência nucleotídica (com bases A, T, C, G e N).
    * 3ª linha: Um  sinal de mais (+), que é simplesmente um separador e pode vir seguido do nome da sequência.
    * 4ª linha: As pontuações de qualidade de cada base da sequência.

<h3> Análise de Qualidade </h3>

- Para analisar a qualidade das sequências do Fastq, vamos utilizar o programa [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/), que fornece uma maneira simples de verificar a qualidade de sequências provenientes de sequenciamento de alto rendimento.

- Com o FastQC instalado, só precisamos chamá-lo seguido de um arquivo fastq:

    <code>fastqc SRR8732219_1.fastq</code>

- O comando acima gera um arquivo <code>.html</code> e um arquivo <code>.zip</code>. O arquivo .html contêm um relatório final com todas as análises de qualidade realizadas. O arquivo <code>.zip</code> é um arquivo compactado das figuras e textos que são resultados das análises. As imagens no arquivo <code>.zip</code> são as mesmas que formam o arquivo <code>.html</code>.

- Pelos gráficos gerados pelo FastQC podemos ter uma ideia geral de como estão as nossas sequências. No site do FastQC são fornecidos exemplos de arquivos html de um metagenoma com [alta qualidade](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/good_sequence_short_fastqc.html) e [baixa qualidade](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/bad_sequence_fastqc.html).

<h3> Trimagem e Filtragem </h3>

- No geral, os metagenomas analisados nesse workshop não precisam de uma limpeza por já terem sido trimados antes de serem adicionados na base de dados, mas vamos aumentar ainda mais a qualidade de um deles.

- A trimagem (corte) de adaptadores e bases de baixa qualidade é uma etapa importante na análise de dados de sequenciamento. Essas regiões são indesejadas por causarem erros ou viéses nas análises.

- Para esta etapa, utilizaremos o programa [cutadapt](https://cutadapt.readthedocs.io/en/stable/). Este programa fornece diversos parâmetros que podem ser utilizados para melhorar a qualidade das nossas sequências. Execute o comando abaixo para ver todas as opções de parâmetros.

    <code>cutadapt --help</code>

- Como todos os nossos metagenomas são paired-end, devemos fornecer dois arquivos de entrada e um segundo arquivo de saída com o parâmetro <code>-p</code> (ou <code>--pair-output</code>). O parâmetro <code>-q</code> (ou <code>--quality-cutoff</code>) será usado para cortar as pontas de baixa qualidade das leituras. O parâmetro <code>-u</code> (ou <code>--cut</code>) remove as bases do início ou do final de cada sequência. Se o número fornecido for positivo, as bases são removidas do início, e se for negativo, são removidas do final. Dessa forma, o comando final fica assim:

    <code>cutadapt -q 20 -u 20 -o buffalo_colombia/SRR11041080_1.q20_2.fastq -p buffalo_colombia/SRR11041080_2.q20_2.fastq buffalo_colombia/SRR11041080_1.fastq buffalo_colombia/SRR11041080_2.fastq</code>
