**Data:** 21/08

# Aula 2: Parte 1 - Introdução ao terminal para análise de sequências biológicas

## Prática síncrona

### Introdução ao Bash

Comandos abordados:

- `pwd`
- `ls`
- `cd`
- `mkdir`
- `cp`
- `mv`
- `rm`
- `cat`
- `head`
- `tail`
- `grep`

### Formato de arquivos

- FASTA

---

# Começando

Durante toda a disciplina utilizaremos o **terminal Linux (Bash)** para organizar arquivos, executar programas e analisar dados biológicos. Nesta primeira prática aprenderemos os principais comandos de navegação e manipulação de arquivos, utilizando um conjunto de proteínas obtido do banco de dados UniProt.

Ao final desta prática você será capaz de:

- navegar entre diretórios;
- criar e organizar pastas;
- copiar, mover e remover arquivos;
- visualizar o conteúdo de arquivos FASTA;
- pesquisar informações utilizando o comando `grep`.

---
> Os comandos abaixo devem ser digitados no terminal. Os trechos iniciados por `#` são comentários explicativos e não precisam ser executados.

## 1. Organizando o diretório da aula

Antes de iniciar qualquer análise, é importante manter os arquivos organizados.

Primeiro, crie um diretório com o seu nome. Todas as atividades da disciplina serão realizadas dentro desse diretório.

Para criar um diretório utilizamos o comando `mkdir`, abreviação de **Make Directory** ("criar diretório").

```bash
mkdir Daisy
```

Substitua **Daisy** pelo seu nome.

---

Agora vamos verificar se o diretório foi criado.

Para listar os arquivos e diretórios utilizamos o comando `ls`, abreviação de **List**.

```bash
ls
```

Você deverá observar algo semelhante a:

```text
Daisy
```

---

Agora entre no diretório recém-criado utilizando o comando `cd`, abreviação de **Change Directory** ("mudar de diretório").

```bash
cd Daisy
```

Para confirmar em qual diretório você está, utilize:

```bash
pwd
```

O comando `pwd` significa **Print Working Directory** e mostra o caminho completo do diretório atual.

---

Vamos verificar se o diretório está vazio.

```bash
ls
```

Como ele acabou de ser criado, nenhuma informação deverá ser exibida.

---

## Outras formas de utilizar o comando `ls`

O comando `ls` possui diversas opções.

### Listagem detalhada

```bash
ls -l
```

Mostra informações detalhadas sobre cada arquivo:

- permissões;
- proprietário;
- tamanho;
- data de modificação.

---

### Mostrar arquivos ocultos

```bash
ls -a
```

Arquivos iniciados por um ponto (`.`) são considerados ocultos no Linux.

---

### Listagem detalhada incluindo arquivos ocultos

```bash
ls -la
```

Combina as opções `-l` e `-a`.

---

### Ordenar por data de modificação

```bash
ls -lrth
```

Onde:

- `-l` → formato longo (*long listing*);
- `-r` → ordem inversa (*reverse*);
- `-t` → ordena pela data de modificação (*time*);
- `-h` → mostra tamanhos em formato legível (*human readable*), como KB, MB e GB.

---

## Criando um diretório para a Aula 2 - parte 1

Dentro do diretório do aluno, vamos criar uma pasta específica para armazenar os arquivos desta aula.

```bash
mkdir aula2_parte1
```

Verifique se ela foi criada.

```bash
ls -lrth
```

---

Agora entre no diretório.

```bash
cd aula2_parte1
```

💡 **Dica**

O terminal possui autocompletar utilizando a tecla **TAB**.

Por exemplo, basta digitar

```bash
cd au
```

e pressionar **TAB**.

O terminal completará automaticamente para

```bash
cd aula2_parte1
```

Esse recurso economiza tempo e evita erros de digitação.

---

## 2. Baixando proteínas do UniProt

Agora utilizaremos um arquivo real contendo proteínas.

1. Acesse [UniProt](https://www.uniprot.org/).
2. Na barra de pesquisa, procure por um organismo, por exemplo *Escherichia coli*.
3. Filtre ou selecione as proteínas **revisadas** (*reviewed*).
4. Clique em **Download** e escolha o formato **FASTA**. Em seguida marque a opção de compactar (*compressed*) e baixe o arquivo.

Após o download, abra a pasta atual do terminal WSL utilizando:

```bash
explorer.exe .
```

O ponto (`.`) representa o diretório atual.

Esse comando facilita copiar ou mover arquivos entre o Windows e o WSL.

Após copiar o arquivo para o diretório da aula, utilize:

```bash
ls
```

Você deverá observar algo semelhante a:

```text
uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta.gz
uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta.gz:Zone.Identifier
```

O arquivo `:Zone.Identifier` pode aparecer quando um arquivo é baixado pelo Windows. Ele contém apenas informações de segurança do sistema operacional e **não faz parte do arquivo FASTA**.

Podemos removê-lo com segurança utilizando:

```bash
rm uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta.gz:Zone.Identifier
```

Confirme que restou apenas o arquivo FASTA compactado.

```bash
ls
```

---

## 3. Descompactando o arquivo

O arquivo foi baixado compactado (`.gz`).

Para descompactá-lo utilizamos o comando `gunzip` (**GNU unzip**).

```bash
gunzip uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta.gz
```

Esse comando remove o arquivo compactado e mantém apenas a versão descompactada.

Caso deseje manter também a versão compactada, utilize:

```bash
gzip -dk uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta.gz
```

onde:

- `-d` → descompacta (*decompress*);
- `-k` → mantém (*keep*) o arquivo original.

---

## 4. Entendendo e visualizando um arquivo FASTA

O formato FASTA armazena sequências biológicas. Cada proteína normalmente possui:

- uma linha de cabeçalho, iniciada por `>`;
- uma ou mais linhas com a sequência de aminoácidos.

Exemplo:

```text
>sp|P0A7V8|RS2_ECOLI 30S ribosomal protein S2 OS=Escherichia coli
MARGKKIGYS...
```

No cabeçalho, `sp` indica uma entrada revisada do UniProt/Swiss-Prot. A sequência abaixo dele é composta pelos códigos de uma letra dos aminoácidos.

Use `cat` (*concatenate*) para imprimir o arquivo inteiro:

```bash
cat uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta
```

Em arquivos grandes, prefira `head` (*cabeça*) e `tail` (*cauda*), que mostram, por padrão, as 10 primeiras e as 10 últimas linhas:

```bash
head uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta
tail uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta
```

Para escolher a quantidade de linhas, use `-n`:

```bash
head -n 20 uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta
tail -n 20 uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta
```

## 5. Renomeando, movendo e removendo arquivos

Um nome curto torna os comandos mais fáceis de ler. `mv` vem de *move* e serve tanto para renomear como para mover arquivos.
Para renomear:

```bash
mv uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta ecoli_reviewed.fasta
ls
```

Para mover um arquivo, informe uma pasta como destino:

```bash
mkdir nova_pasta
mv ecoli_reviewed.fasta nova_pasta/
ls
```

Entre na pasta e confirme que o arquivo foi movido:

```bash
cd nova_pasta
ls
```

`..` representa o diretório imediatamente anterior (a pasta-pai). Portanto, este comando devolve o arquivo à pasta `aula1`:

```bash
mv ecoli_reviewed.fasta ../
```

E este comando retorna para a pasta-pai:

```bash
cd ..
ls
```

Para voltar dois níveis, use `../..`; para três, `../../..`.

Para remover a pasta vazia criada no exemplo:

```bash
rm -r nova_pasta
```

`rm` vem de *remove*. A opção `-r` significa *recursive*: ela percorre a pasta e remove seu conteúdo junto com a própria pasta. Use-a com cuidado, pois a remoção pelo terminal normalmente não vai para a Lixeira.

## 6. Buscando informações com `grep`

`grep` procura linhas que correspondem a um padrão de texto. A forma geral é:

```bash
grep "padrão" arquivo
```

Em um FASTA, cada proteína começa em uma linha de cabeçalho iniciada por `>`. Para mostrar todos os cabeçalhos:

```bash
grep "^>" ecoli_reviewed.fasta
```

O símbolo `^` significa “início da linha”; assim, `^>` busca somente linhas cujo primeiro caractere é `>`.

### Contar proteínas

Como há um cabeçalho por proteína, conte os cabeçalhos com `-c`, de *count* (contar):

```bash
grep -c "^>" ecoli_reviewed.fasta
```

### Procurar uma proteína pelo nome

```bash
grep "polymerase" ecoli_reviewed.fasta
```

Por padrão, a busca diferencia letras maiúsculas e minúsculas. Use `-i` para ignorar essa diferença (*ignore case*):

```bash
grep -i "polymerase" ecoli_reviewed.fasta
```

### Exibir contexto ao redor de uma ocorrência

As sequências vêm após o cabeçalho. `-A` (*after*) mostra linhas **depois** da correspondência; `-B` (*before*) mostra linhas **antes** dela:

```bash
grep -A 1 "rpoB" ecoli_reviewed.fasta
grep -A 10 "rpoB" ecoli_reviewed.fasta
grep -B 1 -A 2 "ATPase" ecoli_reviewed.fasta
```

> Atenção: proteínas longas podem ocupar muitas linhas. `-A 10` não garante a sequência completa; ele apenas mostra as 10 linhas seguintes.

### Procurar mais de um padrão

`-E` ativa expressões regulares estendidas. Nesse modo, `|` significa “ou”:

```bash
grep -E "ATPase|polymerase|helicase" ecoli_reviewed.fasta
```

Assim, o comando mostra linhas que contêm **ATPase**, ou **polymerase** ou **helicase**.

### Mostrar linhas que não contêm um termo

`-v` vem de *invert match*: inverte a seleção e exibe as linhas que **não** correspondem ao padrão.

```bash
grep -v "hypothetical" ecoli_reviewed.fasta
```

Em FASTA, esse comando também mostrará linhas de sequência, pois elas normalmente não possuem a palavra pesquisada. Para trabalhar apenas com cabeçalhos, faça primeiro a seleção de linhas iniciadas em `>`.

## 7. Pesquisando vários genes a partir de uma lista

Crie um arquivo de padrões com `nano`, um editor de texto do terminal:

```bash
nano genes.txt
```

Digite um termo por linha:

```text
rpoB
gyrA
dnaK
```

Para salvar no `nano`, pressione `Ctrl + X`, depois `Y` e `Enter`. Confira o arquivo:

```bash
ls
cat genes.txt
```

Use `grep -f` para ler os padrões de um arquivo (*file*), em vez de digitá-los no comando:

```bash
grep -f genes.txt ecoli_reviewed.fasta
```

Para contar as linhas retornadas, use o operador `|`, chamado *pipe*. Ele envia a saída do comando à esquerda para o comando à direita. `wc` significa *word count* e `-l` conta linhas:

```bash
grep -f genes.txt ecoli_reviewed.fasta | wc -l
```

> Esse total representa linhas encontradas, não necessariamente o número de proteínas, pois um termo pode aparecer em mais de uma linha ou em mais de uma proteína.

## 8. Exemplos finais de filtragem

Contar cabeçalhos que incluem “hypothetical”:

```bash
grep "^>" ecoli_reviewed.fasta | grep -ic "hypothetical"
```

O primeiro `grep` limita a saída aos cabeçalhos; o segundo procura o termo e `-c` conta as linhas. A opção `-i` torna a busca independente de maiúsculas e minúsculas.

Entradas revisadas do UniProt usam o prefixo `sp|`:

```bash
grep "^>sp|" ecoli_reviewed.fasta
grep -c "^>sp|" ecoli_reviewed.fasta
```

Entradas não revisadas usam `tr|` (TrEMBL):

```bash
grep "^>tr|" ecoli_reviewed.fasta
grep -c "^>tr|" ecoli_reviewed.fasta
```

Se o arquivo foi baixado com o filtro **reviewed**, é esperado que a busca por `tr|` retorne zero resultados.


# Aula 2: Parte 2 - Controle de qualidade (QC)

## Prática síncrona

Nesta aula, serão usados dados de sequenciamento genômico de *Lactobacillus fermentum*, identificados pela corrida **SRR11011985**. O objetivo é baixar as leituras, avaliar sua qualidade, remover trechos de baixa qualidade e avaliar novamente o resultado.

> Execute os comandos no terminal WSL/Linux. Linhas iniciadas por `#` são comentários e não precisam ser executadas.

## 1. Criando o diretório da Aula 2

Entre na pasta principal criada na Aula 1. Substitua `Daisy` pelo nome que você escolheu para sua pasta:

```bash
cd Daisy
```

Crie a pasta da segunda aula parte 2 e confirme sua criação:

```bash
mkdir aula2_parte2
ls -lrth
```

Entre na pasta:

```bash
cd aula2_parte2
```

> Dica: use `Tab` para completar nomes automaticamente. Por exemplo, digite `cd au` e pressione `Tab`; o terminal completará o nome `aula2` nesse caso vai haver ambiguidade, entao continue digitando `aula2_parte2` .

## 2. Ambiente virtual e ferramentas

As ferramentas da disciplina foram instaladas em um **ambiente virtual Conda**. Um ambiente virtual é um espaço isolado que reúne programas e suas versões específicas. Isso evita conflitos entre ferramentas e torna as análises mais reprodutíveis.

O primeiro passo é listar os ambientes disponíveis:

```bash
conda env list
```

Você deverá encontrar um ambiente chamado `bioinfo`. Para utilizá-lo, ative-o com o comando:

```bash
conda activate bioinfo
```

Após a ativação, o início da linha de comando mudará de `(base)` para `(bioinfo)`:

(base) daisy@Daisy:~/Daisy/aula2$

↓

(bioinfo) daisy@Daisy:~/Daisy/aula2$

Essa mudança indica que o ambiente bioinfo está ativo e que todas as ferramentas instaladas nele (como FastQC, Trimmomatic, SPAdes, entre outras) estarão disponíveis para uso durante a aula.

## 3. Arquivos de sequenciamento e formato FASTQ

Os dados de sequenciamento serão obtidos no formato FASTQ, que armazena tanto a sequência de nucleotídeos quanto a qualidade de cada base sequenciada. Cada leitura (read) é representada por quatro linhas:

1. Um identificador da leitura, iniciado pelo caractere `@`;
2. A sequência de nucleotídeos (A, C, G, T e, ocasionalmente, N);
3. Uma linha iniciada por `+`, que separa a sequência dos valores de qualidade;;
4. Uma sequência de caracteres ASCII que representa a qualidade (escore Phred) de cada base da leitura.

Em experimentos de sequenciamento *paired-end* (neste nosso caso), cada fragmento de DNA é sequenciado a partir de suas duas extremidades, gerando duas leituras correspondentes para o mesmo fragmento. Por esse motivo, são gerados dois arquivos FASTQ:

- R1 (ou `_1`: contém as leituras da primeira extremidade (**forward**).
- R2 (ou `_2`): contém as leituras da segunda extremidade (**reverse**).

As leituras presentes em R1 e R2 são pareadas, ou seja, a primeira leitura de R1 corresponde à primeira leitura de R2, a segunda de R1 corresponde à segunda de R2, e assim sucessivamente.

### Scores Phred

A qualidade de uma base é expressa pelo score Phred (`Q`):

```text
Q = -10 × log10(P)
```

Nessa fórmula, `P` é a probabilidade de a base estar incorreta. Quanto maior o score, menor a chance de erro. Como referência:

| Score Phred | Probabilidade de erro | Interpretação |
|---:|---:|---|
| Q20 | 1 em 100 | 99% de acurácia |
| Q30 | 1 em 1.000 | 99,9% de acurácia |
| Q40 | 1 em 10.000 | 99,99% de acurácia |

## 4. Baixando os dados — opção 1: ENA

O **ENA** (*European Nucleotide Archive*) é um banco público de dados de sequenciamento mantido pelo EMBL-EBI.

1. Acesse [European Nucleotide Archive (ENA)](https://www.ebi.ac.uk/ena/browser/home).
2. No campo **Enter accession**, informe `SRR11011985`.
3. Clique em **View**.
4. Na seção **Generated FASTQ files: FTP**, selecione os arquivos *forward* e *reverse*.
5. Clique em **Get download script**.
6. Copie o link referente ao arquivo R1, cole-o no terminal e pressione `Enter`.
7. Espere o download terminar e repita o processo para o arquivo R2.
8. Confirme os arquivos baixados:

```bash
ls -lh
```

Os arquivos podem estar compactados e, nesse caso, terminam em `.fastq.gz`. Muitas ferramentas, incluindo o FastQC, conseguem lê-los diretamente sem descompactação.

## 5. Baixando os dados — opção 2: SRA Toolkit

Se o ENA estiver indisponível, use o **SRA Toolkit**, um conjunto de programas do NCBI para obter e converter dados do Sequence Read Archive (SRA).

Primeiro, baixe os dados brutos da corrida:

```bash
prefetch SRR11011985 -O .
```

- `prefetch`: baixa e armazena localmente os dados do SRA.
- `SRR11011985`: identificador de acesso (*accession*) da corrida.
- `-O .`: salva o resultado no diretório atual; `.` representa a pasta em que você está.

O SRA armazena os dados em um formato binário próprio, `.sra`. Para obter arquivos FASTQ, faça a conversão:

```bash
fasterq-dump SRR11011985 --split-files --threads 4
```

- `fasterq-dump`: converte dados SRA em FASTQ.
- `--split-files`: separa leituras pareadas em dois arquivos.
- `--threads 4`: permite usar até quatro núcleos de processamento.

O resultado esperado é:

```text
SRR11011985_1.fastq
SRR11011985_2.fastq
```

Verifique se os arquivos foram criados e confira o espaço ocupado:

```bash
ls -lh
du -sh *
```

`du -sh *` mostra o espaço usado por cada item: `-s` apresenta somente o total, `-h` usa unidades legíveis e `*` significa “todos os arquivos e pastas visíveis da pasta atual”.

> Use apenas uma das formas de download. Se você baixou os FASTQ pelo ENA, não é necessário repetir o processo com SRA Toolkit.

## 6. Visualização rápida dos arquivos FASTQ

Veja as duas primeiras leituras do arquivo R1. Cada leitura ocupa quatro linhas; por isso, oito linhas correspondem a duas leituras:

```bash
head -n 8 SRR11011985_1.fastq
```

Faça o mesmo para R2:

```bash
head -n 8 SRR11011985_2.fastq
```

Se os arquivos estiverem compactados (`.fastq.gz`), use `zcat` para visualizar sem descompactar:

```bash
zcat SRR11011985_1.fastq.gz | head -n 8
```

O símbolo `|`, chamado *pipe*, envia a saída de `zcat` para `head`. Assim, apenas as primeiras linhas são exibidas.

## 7. Controle de qualidade inicial com FastQC

O [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) produz relatórios sobre a qualidade dos dados, composição de bases, presença de adaptadores e outros indicadores.

Confirme que ele está disponível no ambiente:

```bash
fastqc --version
```

Crie uma pasta para os relatórios das leituras brutas e execute a análise:

```bash
mkdir -p 1_fastqc_brutos
fastqc -t 4 -o 1_fastqc_brutos SRR11011985_1.fastq SRR11011985_2.fastq
```

- `-t 4`: usa até quatro *threads*.
- `-o 1_fastqc_brutos`: define a pasta de saída.
- Os dois nomes finais são os arquivos que serão analisados.

> Se você baixou arquivos `.fastq.gz`, use esses nomes no comando; o FastQC aceita arquivos compactados.

Veja os resultados:

```bash
ls 1_fastqc_brutos/
```

Serão gerados, para cada arquivo, um relatório `.html` e um arquivo `.zip`. Abra a pasta atual no Explorador de Arquivos do Windows:

```bash
cd 1_fastqc_brutos
explorer.exe .
```

Abra os dois arquivos HTML no navegador. Avalie principalmente:

- **Per base sequence quality**: qualidade ao longo das posições da leitura;
- **Per sequence quality scores**: distribuição da qualidade média das leituras;
- **Per base sequence content**: proporção de A, T, C e G em cada posição;
- **Adapter content**: presença de sequências de adaptadores;
- **Overrepresented sequences**: sequências que aparecem com frequência inesperadamente alta.

Um aviso ou falha no FastQC não significa automaticamente que os dados são inutilizáveis. O resultado deve ser interpretado no contexto do experimento e usado para decidir quais etapas de filtragem são necessárias.

Volte à pasta da aula:

```bash
cd ..
```

## 8. Trimagem com Trimmomatic

**Trimagem** é a remoção de bases ou leituras de baixa qualidade e, quando necessário, de adaptadores. O objetivo é reduzir erros que podem atrapalhar etapas posteriores, como montagem e anotação.

Crie a pasta de saída:

```bash
mkdir 2_trimmomatic
```

Verifique a instalação do Trimmomatic:

```bash
conda list trimmomatic
```

Execute a trimagem das leituras pareadas:

```bash
trimmomatic PE -threads 4 \
  SRR11011985_1.fastq SRR11011985_2.fastq \
  2_trimmomatic/SRR11011985_1_paired.fastq 2_trimmomatic/SRR11011985_1_unpaired.fastq \
  2_trimmomatic/SRR11011985_2_paired.fastq 2_trimmomatic/SRR11011985_2_unpaired.fastq \
  HEADCROP:10 SLIDINGWINDOW:4:20 MINLEN:50
```

As barras invertidas (`\`) apenas dividem um comando longo em várias linhas para facilitar a leitura. O terminal executa tudo como um único comando.

### Entendendo o comando

| Elemento | Função |
|---|---|
| `PE` | Indica dados *paired-end*. |
| `-threads 4` | Usa até quatro núcleos de processamento. |
| `SRR11011985_1.fastq` | Arquivo de entrada R1 (*forward*). |
| `SRR11011985_2.fastq` | Arquivo de entrada R2 (*reverse*). |
| `_1_paired.fastq` e `_2_paired.fastq` | Leituras cujos dois pares permaneceram após a filtragem. |
| `_1_unpaired.fastq` | Leituras R1 que permaneceram, mas cujo par R2 foi descartado. |
| `_2_unpaired.fastq` | Leituras R2 que permaneceram, mas cujo par R1 foi descartado. |
| `HEADCROP:10` | Remove as 10 primeiras bases de cada leitura. |
| `SLIDINGWINDOW:4:20` | Examina uma janela de 4 bases e corta a leitura quando a qualidade média cai abaixo de Q20. |
| `MINLEN:50` | Descarta leituras que ficam com menos de 50 bases após os cortes. |

Os resultados podem ser interpretados assim:

| Mensagem do Trimmomatic | Arquivo(s) gerado(s) | Significado |
|---|---|---|
| `Both Surviving` | `_1_paired.fastq` e `_2_paired.fastq` | Os dois membros do par passaram nos filtros. |
| `Forward Only Surviving` | `_1_unpaired.fastq` | Apenas a leitura forward passou. |
| `Reverse Only Surviving` | `_2_unpaired.fastq` | Apenas a leitura reverse passou. |
| `Dropped` | — | Nenhuma leitura do par passou nos filtros. |

Para análises *paired-end* posteriores, são usados os dois arquivos `paired`. Os arquivos `unpaired` não devem ser misturados aos pares sem planejamento.

### Outros filtros úteis do Trimmomatic

Os filtros devem ser definidos a partir do relatório do FastQC e do desenho experimental. Alguns exemplos são:

| Parâmetro | Função |
|---|---|
| `ILLUMINACLIP:arquivo_adaptadores.fa:2:30:10` | Procura e remove adaptadores descritos em um arquivo FASTA. |
| `HEADCROP:n` | Remove `n` bases do início de cada leitura. |
| `TAILCROP:n` | Remove `n` bases do final de cada leitura. |
| `CROP:n` | Mantém somente as primeiras `n` bases. |
| `LEADING:q` | Remove bases iniciais com qualidade abaixo de `q`. |
| `TRAILING:q` | Remove bases finais com qualidade abaixo de `q`. |
| `SLIDINGWINDOW:tamanho:qualidade` | Aplica corte conforme a qualidade média de uma janela. |
| `MINLEN:n` | Descarta leituras menores que `n` bases após a trimagem. |

Confira os arquivos criados:

```bash
ls 2_trimmomatic/
```

## 9. Controle de qualidade após a trimagem

Faça uma segunda análise com FastQC, agora usando as leituras pareadas que passaram pelos filtros:

```bash
mkdir -p 3_fastqc_trimmed  #fazer um novo diretório para armazenar resultados da nova análise FASTQC
fastqc -t 4 -o 3_fastqc_trimmed \   #Rodando o FASTQC
  2_trimmomatic/SRR11011985_1_paired.fastq \
  2_trimmomatic/SRR11011985_2_paired.fastq
```

Abra os novos relatórios:

```bash
cd 3_fastqc_trimmed
explorer.exe .
```

Compare os relatórios **antes** (`1_fastqc_brutos`) e **depois** (`3_fastqc_trimmed`) da trimagem. Observe se houve melhora na qualidade das bases, redução de adaptadores e diminuição de problemas no final das leituras. Também verifique quantas leituras permaneceram no resumo exibido pelo Trimmomatic: uma filtragem excessiva pode remover uma parcela importante dos dados.

---













