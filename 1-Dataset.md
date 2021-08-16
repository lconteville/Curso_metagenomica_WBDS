<h3> Neste curso serão utilizados dados metagenômicos disponíveis em bancos de dados: </h3>

- **Microbioma Intestinal** de Indígena da Amazônia Brasileira ([Conteville et al, 2019](https://www.frontiersin.org/articles/10.3389/fmicb.2019.01743/full))
- **Microbioma de Solo** de Caatinga do Nordeste Brasileiro ([Lacerda-Júnior et al, 2019](https://www.frontiersin.org/articles/10.3389/fmicb.2019.00648/full))
- **Microbioma Aquático** de Praia no Uruguai ([Fresia et al, 2019](https://microbiomejournal.biomedcentral.com/articles/10.1186/s40168-019-0648-z))
- **Microbioma de Rúmen** de Búfalo Colombiano ([Aguilar-Marin et al, 2020](https://bmcmicrobiol.biomedcentral.com/articles/10.1186/s12866-020-02037-6#Sec8))

<h3> Download dos Metagenomas </h3>

- Para conseguir baixar e analisar esses metagenomas, primeiramente precisamos do número de acesso do metagenoma (ex: SRR8732219) de interesse ou do projeto (ex: BioProject PRJNA527208) na base de dados que eles estão depositados. Normalmente esses números são apresentados no artigo a qual eles estão associados.
- Se os metagenomas foram depositados no NCBI, eles ficam armazenados no [Sequence Read Archive (SRA)](https://www.ncbi.nlm.nih.gov/sra), um repositório público para dados de sequenciamento de DNA.
- Para baixar algum dado do SRA, podemos usar o [SRA toolkit](https://ncbi.github.io/sra-tools/), é uma coleção de ferramentas e bibliotecas para este fim. Nesta coleção existe a ferramenta <code>fasterq-dump</code>, utilizada para baixar os metagenomas que serão analisados neste workshop.
- Com o SRA toolkit instalado, só precisamos chamar a ferramenta fasterq-dump seguinda do número de acesso do metagenoma de interesse:
    
    <code>fasterq-dump SRR8732219</code>
    
- O comando acima gera dois arquivos <code>.fastq</code>, que referem-se às sequências pareadas do metagenoma de interesse.

<h3> Arquivos Fastq </h3>

- Os Fastq são arquivos de texto em que as informações sobre cada sequência estão dispostas em 4 linhas:
    * 1ª linha: O nome da sequência.
    * 2ª linha: A sequência nucleotídica (com bases A, T, C, G e N).
    * 3ª linha: Um  sinal de mais (+), que é simplesmente um separador e pode vir seguido do nome da sequência.
    * 4ª linha: As pontuações de qualidade de cada base da sequência.
- Para ler um arquivo pelo terminal, podemos usar o comando <code>head</code>. Esse comando mostra no terminal o conteúdo das 10 primeiras linhas de um arquivo.

    <code>head SRR8732219_1.fastq</code>

<h3> Análise de Qualidade </h3>

<h3>
