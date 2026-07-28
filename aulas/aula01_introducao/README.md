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

# Organizando o diretório de trabalho

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

## Criando um diretório para a Aula 1

Dentro do diretório do aluno, vamos criar uma pasta específica para armazenar os arquivos desta aula.

```bash
mkdir aula1
```

Verifique se ela foi criada.

```bash
ls -lrth
```

---

Agora entre no diretório.

```bash
cd aula1
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
cd aula1
```

Esse recurso economiza tempo e evita erros de digitação.

---

# Obtendo um arquivo FASTA no UniProt

Agora utilizaremos um arquivo real contendo proteínas.

1. Acesse o **UniProt**.

2. Na barra de pesquisa, procure por:

```
Escherichia coli
```

3. Clique em **Download**.

4. Selecione:

- **Format:** FASTA (Canonical)
- **Compressed:** Yes

5. Faça o download do arquivo.

> **Inserir aqui uma captura de tela da página de download do UniProt.**

---

Após o download, abra a pasta atual do terminal no Explorador de Arquivos do Windows utilizando:

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

# Descompactando o arquivo

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

## Próxima etapa

Agora que temos um arquivo FASTA, aprenderemos como visualizar seu conteúdo utilizando os comandos `cat`, `head` e `tail`, entenderemos a estrutura do formato FASTA e utilizaremos o comando `grep` para pesquisar proteínas e extrair informações do arquivo.


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
