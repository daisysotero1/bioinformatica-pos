# Aula 1 – Introdução à Bioinformática

**Data:** 19/08

## Conteúdo

- Apresentação da disciplina
- O que é Bioinformática e suas aplicações
- Fluxo geral de uma análise bioinformática
  - FASTQ → Controle de qualidade → Montagem/Alinhamento → Anotação → Interpretação
- Tipos de dados ômicos
- Tecnologias de sequenciamento
- Bancos de dados biológicos (NCBI, UniProt, KEGG e PDB)

---

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

Primeiro, crie uma pasta com seu nome. `mkdir` vem de *make directory* (criar diretório).

```bash
mkdir Daisy
```

Para ver o que existe na pasta atual, use `ls`, de *list* (listar):

```bash
ls
```

Entre na pasta criada com `cd`, de *change directory* (mudar de diretório):

```bash
cd Daisy
ls
```

No início ela estará vazia. Algumas variações úteis de `ls` são:

```bash
ls -la
ls -lrth
```

- `-l`: mostra uma listagem detalhada (permissões, proprietário, tamanho e data).
- `-a`: inclui arquivos ocultos, cujos nomes começam com `.`.
- `-r`: inverte a ordem da listagem.
- `-t`: ordena por data de modificação.
- `-h`: mostra tamanhos de modo legível, como `2K` ou `15M`.

Crie uma subpasta para a primeira aula e entre nela:

```bash
mkdir aula1
cd aula1
ls -lrth
```

> Dica: digite o começo de um nome de arquivo ou pasta e pressione `Tab` para completá-lo automaticamente.

## 2. Baixando proteínas do UniProt

1. Acesse [UniProt](https://www.uniprot.org/).
2. Pesquise um organismo, por exemplo *Escherichia coli*.
3. Filtre ou selecione as proteínas **revisadas** (*reviewed*).
4. Em **Download**, escolha o formato **FASTA**, marque a opção de compactar (*compressed*) e baixe o arquivo.

No WSL, o comando abaixo abre a pasta atual no Explorador de Arquivos do Windows, o que facilita copiar o arquivo baixado para ela:

```bash
explorer.exe .
```

Confira o download:

```bash
ls
```

Às vezes aparece também um arquivo com o sufixo `:Zone.Identifier`. Ele é uma marca de segurança criada pelo Windows para indicar que o arquivo veio da internet. Não é parte dos dados FASTA e pode ser removido:

```bash
rm uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta.gz:Zone.Identifier
```

## 3. Descompactando o arquivo

Arquivos terminados em `.gz` estão compactados com gzip. `gunzip` os descompacta:

```bash
gunzip uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta.gz
```

Esse comando remove a cópia `.gz` e deixa a versão `.fasta`. Para **manter a cópia compactada** e criar também a versão descompactada, use:

```bash
gzip -dk uniprotkb_Escherichia_coli_AND_reviewed_2026_07_28.fasta.gz
```

- `-d`: descompacta (*decompress*).
- `-k`: mantém (*keep*) o arquivo original.

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

Um nome curto torna os comandos mais fáceis de ler. `mv` vem de *move* e serve tanto para renomear como para mover arquivos:

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

Assim, o comando mostra linhas que contêm **ATPase**, **polymerase** ou **helicase**.

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

## Atividade assíncrona

### Leitura de artigo científico

Após a leitura do artigo, responda às seguintes questões:

1. Qual era o objetivo do estudo?
2. Qual(is) organismo(s) foi(foram) analisado(s)?
3. Que tipo de dado foi utilizado (genoma, transcriptoma, metagenoma, proteoma etc.)?
4. Quais ferramentas de bioinformática foram utilizadas?
5. Quais bancos de dados foram utilizados?
6. Quais foram os principais resultados biológicos obtidos?

---
