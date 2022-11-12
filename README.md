# Anotação de variantes somáticas utilizando VEP ensembl-vep-105.0
Nosso pipeline tem como intuito realizar anotações de arquivos .VCF somáticos para fins didáticos.
Todos os testes realizados foram realizados no ambiente de execução do GoogleColaboratory.

Para a realização dos testes a seguir temos um arquivo .VCF disponível para os testes.

link para acesso do ambiente Colab: https://colab.research.google.com

## Preparando o ambiente Colab para o início dos testes:

Ao abrir o Ambiente de execução Colab conecte-se ao seu Drive utilizando o seguinte comando:
```python
from google.colab import drive
drive.mount('/content/drive')
```
Com um ambiente pronto podemos partir para a instalação das ferramentas necessárias para nossa pipeline.

## Realizando a instalação da ferramenta VEP ensembl-vep-105.0
Para o dowload e instalação das ferramentas VEP utilizaremos o código a baixo :
```bash
%%bash
sudo apt install unzip curl git libmodule-build-perl libdbi-perl libdbd-mysql-perl build-essential zlib1g-dev
wget -c https://github.com/Ensembl/ensembl-vep/archive/refs/tags/105.0.tar.gz
tar -zxvf 105.0.tar.gz
cd ensembl-vep-105.0
./INSTALL.pl --NO_UPDATE 
```
## Vamos realizar uma breve análise do código acima para um melhor entendimento do que está sendo feito :grinning: :


>Segunda linha do código referente a instalação de dependências

Dependências utilizadas:
Dependência|Descrição
---|---
libmodule-build-perl|Infraestrutura para a construção e instalação de módulos Perl
libdbi-perl|DBI (DataBase Interface) é uma infra-estrutura Perl que fornece uma interface comum para acessar vários bancos de dados nos bastidores de uma maneira uniforme
libdbd-mysql-perl|É uma interface entre a linguagem de programação Perl e a API de programação MySQL que vem junto ao sistema de gerenciamento dde banco de dados relacional Maria DB/MySQL
build-essential|Lista informativa de pacotes "build-essential"
zlib1g-dev|O zlib é uma biblioteca que implementa o método de compressão deflate encontrado no gzip e no PKZIP. Este pacote inclui os arquivos de suporte ao desenvolvimento.


>Terceira linha o código

Estaremos utilizando o `wget` para fazer a busca e dowload da nossa ferramenta presentes no seguinte link: https://github.com/Ensembl/ensembl-vep/archive/refs/tags/105.0.tar.gz



>Quarta linha do código

Como nosso arquivo adiquirio está compactado, utilizaremos o comando linux `tar` para extrair os arquivos do programa. Para isso adicionamos alguns rrequisitos utilizano em sequência o comando `-zxvf`.

Para um maior entendimento das opções o comando `tar` separei um link que detalha suas opções e aplicabilidades em diversas situações :wink:: https://www.geeksforgeeks.org/tar-command-linux-examples/ 

>Quinta linha do código

Com os arquivos da ferramenta VEP disponíveis no seu ambiente de execução, utilizaremos o comando `cd` para nos deslocarmos até a pasta extraida que obtivemos com o comando anterior.

>Sexta linha do código

Estando dentro da pasta da ferramenta VEP poderemos enfim executar seu instalador o arrquivo "INTALL.pl".

Reparem que utilizamos o comando `--NO_UPDATE` para que o sistema não busque por atualizações, dessa forma executando somente o que foi solicitado.

*Atualmente temos disponíveis novas versões do VEP, porém para está pipeline a versão utilizada foi a 105.0 (utilizar outra versão pode gerar resultados diferentes ou gerar erros)*

## Dando continuidade ao nosso código
Seguiremos com um teste para ver se nossa ferramenta está pronta para seguir
```bash
%%bash 
cd ensembl-vep-105.0/
./vep
```
Caso você obtenha como resultado a documentação o VEP significa que estamos no caminho certo (segue imagem do resultado esperado)
![resulltado_vep](https://user-images.githubusercontent.com/104692193/201490583-345be930-6a0e-4738-bcb4-62067c62d383.png)

Para executar a ferramenta VEP utilizaremos o seguinte código:

Segue o link da documentação da ferramenta VEP: http://www.ensembl.org/info/docs/tools/vep/script/vep_options.html 
```bash
%%bash
./ensembl-vep-105.0/vep  \
  --fork 3 \
  -i /content/drive/Shareddrives/T4-2022/homo_sapiens_refseq/105_GRCh37/WP312.filtered.vcf.gz \
  -o WP312.filtered.vcf.tsv \
  --dir_cache /content/drive/Shareddrives/T4-2022/ \
  --fasta /content/drive/Shareddrives/T4-2022/homo_sapiens_refseq/Homo_sapiens_assembly19.fasta \
  --cache --offline --assembly GRCh37 --refseq  \
	--pick --pick_allele --force_overwrite --tab --symbol --check_existing --variant_class --everything --filter_common \
  --fields "Uploaded_variation,Location,Allele,Existing_variation,HGVSc,HGVSp,SYMBOL,Consequence,IND,ZYG,Amino_acids,CLIN_SIG,PolyPhen,SIFT,VARIANT_CLASS,FREQS" \
  --individual all
```

## Novamente para melhor entendimento iremos dar uma breve desmembrada no código

Para que fique claro, o código acima foi baseado no meu ambiente de execução com um driver compartilhado, então é necessário realizar os ajustes adequados nos seguintes caminhos: 
- Segunda linha do código, informando em qual diretório a ferramenta VEP está localizada;
- Quarta linha, onde o `-i` representa o input, então deve ser preenchido com o caminho do arquivo .VCF que será processado;
- Sexta linha, onde o `--dir_cache` representa o diretório do cache;
- Sétima linha, onde o `--fasta` representa o caminho onde sua referência está salva (Essa referência pode ser obtida no site da UCSC: https://genome.ucsc.edu) ;

A linha de `--fields` pode ser preenchida com uma grande quantidade de itens para sua análise, gerando a possibilidade de selecionar as opções mais relevantes (possui diversos itens para serem listados como: Allele, Location, Consequence, etc...)

A lista de itens que podem ser introduzidos nesse campo estão disponíveis na documentação da ferramenta: http://www.ensembl.org/info/docs/tools/vep/vep_formats.html#output

Como resultado teremos um arquivo anotado .TSV :+1:

## Para verificar o Resultado utilizaremos uma tabela pelo dataframe Pandas

Iremos importa a biblioteca pandas no ambiente de execução

```python
import pandas as pd
```
Com a biblioteca importada podemos gerar um dataframe com o nosso arquivo .TSV

```python
tabela = pd.read_csv('/content/WP312.filtered.vcf.tsv', sep='\t', skiprows=38)
df = pd.DataFrame(tabela)
df
```
Teremos como Output a seguinte tabela:
![Captura de tela 2022-11-12 165210](https://user-images.githubusercontent.com/104692193/201492128-8302485d-6df6-489a-8aa8-8d61258cfcef.png)

