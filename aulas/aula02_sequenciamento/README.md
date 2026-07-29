# Aula 2 – Controle de qualidade de sequenciamento

**Data:** 21/08

## Conteúdo - parte 1

- Formatos de arquivos
- Qualidade de sequenciamento
- Scores Phred
- Adaptadores
- Controle de qualidade
- Trimagem

## Conteúdo - parte 2

- Conceitos e teoria: montagem de genomas e predição de genes
- Anotação funcional

---

## Prática síncrona - parte 1

Nesta aula, serão usados dados de sequenciamento genômico de *Lactobacillus fermentum*, identificados pela corrida **SRR11011985**. O objetivo é baixar as leituras, avaliar sua qualidade, remover trechos de baixa qualidade e avaliar novamente o resultado.

> Execute os comandos no terminal WSL/Linux. Linhas iniciadas por `#` são comentários e não precisam ser executadas.

## 1. Criando o diretório da Aula 2

Entre na pasta principal criada na Aula 1. Substitua `Daisy` pelo nome que você escolheu para sua pasta:

```bash
cd Daisy
```

Crie a pasta da segunda aula e confirme sua criação:

```bash
mkdir aula2
ls -lrth
```

Entre na pasta:

```bash
cd aula2
```

> Dica: use `Tab` para completar nomes automaticamente. Por exemplo, digite `cd au` e pressione `Tab`; o terminal completará o nome `aula2` quando não houver ambiguidade.

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

Essa mudança indica que o ambiente bioinfo está ativo e que todas as ferramentas instaladas nele (como FastQC, MultiQC, Trimmomatic, SPAdes, BUSCO, entre outras) estarão disponíveis para uso durante a aula.

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

# Prática síncrona - parte 2

Após avaliar a qualidade dos dados e realizar a trimagem das leituras, o próximo passo em muitos projetos de sequenciamento é a **montagem do genoma** (*genome assembly*).

A montagem consiste em reconstruir sequências maiores (*contigs*) a partir das leituras obtidas no sequenciamento. Como as tecnologias atuais geram milhões de pequenos fragmentos de DNA, utilizamos programas capazes de identificar regiões de sobreposição entre essas leituras para reconstruir a sequência original do genoma.

Nesta aula utilizaremos o **SPAdes** (*St. Petersburg genome assembler*), um dos montadores mais utilizados para genomas bacterianos.

> Execute os comandos no terminal WSL/Linux. Linhas iniciadas por `#` são comentários e não precisam ser executadas.

---

## 1. Voltando para o diretório da Aula 2

Primeiro, volte para o diretório principal da Aula 2:

```bash
cd ..
```

A estrutura da pasta deverá estar semelhante a esta:

```text
(bioinfo) daisy@Daisy:~/Daisy/aula2$ ls

1_fastqc_brutos
2_trimmomatic
3_fastqc_trimmed
SRR11011985
SRR11011985_1.fastq
SRR11011985_2.fastq

(bioinfo) daisy@Daisy:~/Daisy/aula2$
```

---

## 2. Criando a pasta da montagem

Crie um diretório para armazenar todos os arquivos produzidos pelo SPAdes:

```bash
mkdir 4_assembly
```

Organizar os resultados em diretórios separados facilita a navegação pelos arquivos e evita misturar resultados de diferentes etapas da análise.

---

## 3. Verificando a instalação do SPAdes

Antes de iniciar a montagem, confirme se o SPAdes está instalado corretamente:

```bash
spades.py -v
```

O resultado esperado é semelhante a:

```text
(bioinfo) daisy@Daisy:~/Daisy/aula2$ spades.py -v
SPAdes genome assembler v4.3.0
(bioinfo) daisy@Daisy:~/Daisy/aula2$
```

> **Importante:** para que o comando funcione, o ambiente virtual `bioinfo` deve estar ativado. Caso o nome `(bioinfo)` não apareça no início da linha de comando, ative o ambiente novamente:

```bash
conda activate bioinfo
```

---

## 4. Modos de execução do SPAdes

Assim como outras ferramentas utilizadas ao longo da disciplina, o SPAdes possui diversos parâmetros que modificam seu funcionamento. Eles podem ser consultados utilizando a opção `--help`:

```bash
spades.py --help
```

Entre as opções mais utilizadas estão:

| Parâmetro | Função |
|-----------|--------|
| `--isolate` | Recomendado para dados de organismos isolados com alta cobertura, como a maioria dos genomas bacterianos. Este será o modo utilizado nesta aula. |
| `--meta` | Utilizado para montagem de dados metagenômicos, provenientes de comunidades de microrganismos. |
| `--rna` | Utilizado para dados de RNA-Seq. |
| `--sc` | Utilizado para dados obtidos por sequenciamento de célula única (*single-cell*). |
| `--only-error-correction` | Executa apenas a etapa de correção de erros das leituras, sem realizar a montagem. |
| `--only-assembler` | Executa apenas a montagem, assumindo que as leituras já foram corrigidas. |
| `--careful` | Reduz o número de mismatches e pequenas inserções/deleções (*indels*) na montagem. **Na versão 4.3.0 do SPAdes, esse parâmetro não pode ser utilizado juntamente com `--isolate`.** |

Nesta aula utilizaremos o parâmetro `--isolate`, pois os dados analisados correspondem ao genoma de uma bactéria isolada (*Lactobacillus fermentum*), exatamente o tipo de dado para o qual esse modo foi desenvolvido.

---

## 5. Executando a montagem (não execute este comando neste momento)

O comando básico para executar o SPAdes é:

```bash
spades.py \
    --isolate \
    -1 2_trimmomatic/SRR11011985_1_paired.fastq \
    -2 2_trimmomatic/SRR11011985_2_paired.fastq \
    -o 4_assembly \
    -t 4 \
    -k auto
```

Entretanto, montagens podem demorar vários minutos ou até horas, dependendo do tamanho do genoma e da quantidade de dados.

Se executarmos o comando normalmente, o terminal permanecerá ocupado durante todo o processamento. Caso o terminal seja fechado, a montagem será interrompida.

Para evitar esse problema, utilizaremos o comando `nohup`, que permite executar programas em segundo plano.

---

## 6. Executando o SPAdes com `nohup` (execute este comando neste momento)

O `nohup` (*no hang up*) permite que um programa continue sendo executado mesmo após o fechamento do terminal.

Além disso, utilizaremos recursos do Linux para salvar todas as mensagens produzidas durante a execução em um arquivo de log.

```bash
nohup spades.py \
    --isolate \
    -1 2_trimmomatic/SRR11011985_1_paired.fastq \
    -2 2_trimmomatic/SRR11011985_2_paired.fastq \
    -o 4_assembly \
    -t 4 \
    -k auto \
    > spades.log 2>&1 &
```

Após executar o comando, o terminal será liberado imediatamente, enquanto a montagem continuará sendo executada em segundo plano.

---

## 7. Entendendo o comando

| Elemento | Função |
|----------|--------|
| `nohup` | Mantém o programa em execução mesmo que o terminal seja fechado. |
| `spades.py` | Executa o montador SPAdes. |
| `--isolate` | Indica ao SPAdes que os dados correspondem ao genoma de um organismo isolado, modo recomendado para a maioria dos genomas bacterianos. |
| `-1` | Arquivo FASTQ contendo as leituras *forward* (R1). |
| `-2` | Arquivo FASTQ contendo as leituras *reverse* (R2). |
| `-o 4_assembly` | Define o diretório onde todos os resultados da montagem serão armazenados. |
| `-t 4` | Utiliza até quatro núcleos de processamento (*threads*). |
| `-k auto` | Permite que o SPAdes escolha automaticamente os tamanhos de *k-mers* mais adequados para o comprimento das leituras. |
| `> spades.log` | Redireciona todas as mensagens exibidas na tela para o arquivo `spades.log`. |
| `2>&1` | Faz com que as mensagens de erro também sejam gravadas no mesmo arquivo de log. |
| `&` | Executa o programa em segundo plano, liberando o terminal para outros comandos. |

Durante a execução, é possível acompanhar o progresso observando o arquivo de log:

```bash
tail -f spades.log
```

Para interromper a visualização do log, pressione:

```text
Ctrl + C
```

Isso **não interrompe** a montagem; apenas fecha a visualização do arquivo.

---

## Organização esperada da pasta `aula2`

```text
aula2/
├── SRR11011985_1.fastq
├── SRR11011985_2.fastq
├── 1_fastqc_brutos/
│   ├── SRR11011985_1_fastqc.html
│   ├── SRR11011985_1_fastqc.zip
│   ├── SRR11011985_2_fastqc.html
│   └── SRR11011985_2_fastqc.zip
├── 2_trimmomatic/
│   ├── SRR11011985_1_paired.fastq
│   ├── SRR11011985_1_unpaired.fastq
│   ├── SRR11011985_2_paired.fastq
│   └── SRR11011985_2_unpaired.fastq
├── 3_fastqc_trimmed/
│   ├── SRR11011985_1_paired_fastqc.html
│   ├── SRR11011985_1_paired_fastqc.zip
│   ├── SRR11011985_2_paired_fastqc.html
│   └── SRR11011985_2_paired_fastqc.zip
└── 4_assembly/
    ├── contigs.fasta
    ├── scaffolds.fasta
    ├── assembly_graph.fastg
    ├── assembly_graph_with_scaffolds.gfa
    ├── before_rr.fasta
    ├── corrected/
    ├── K21/
    ├── K33/
    ├── K55/
    ├── K77/
    ├── misc/
    ├── params.txt
    ├── spades.log
    └── warnings.log
```

> A quantidade de arquivos e pastas pode variar dependendo da versão do SPAdes e dos parâmetros utilizados. Os arquivos mais importantes para as próximas etapas serão `contigs.fasta` e `scaffolds.fasta`, que contêm as sequências montadas.

---

# Prática assíncrona

### Relatório de avaliação da qualidade de dados de sequenciamento e impacto da trimagem

Após realizar análises da parte 1 da aula síncrona, responda às seguintes questões:

1. Qual era o objetivo da atividade?
2. Apresente as figuras do FastQC (antes e após a trimagem) e descreva os principais resultados observados. 
3. Comparação entre os resultados antes e após a trimagem: Quais métricas já apresentavam boa qualidade antes da trimagem? Quais métricas melhoraram após a trimagem? Alguma métrica permaneceu indicando problemas? A trimagem alterou significativamente o comprimento das reads? Como isso pode impactar análises posteriores?

---
