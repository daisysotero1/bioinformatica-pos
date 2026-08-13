# Aula 3 – Avaliação da qualidade da montagem

## Conteúdo

- Por que precisamos montar o genoma?
- Cobertura
- Montagem de novo × referência
- Grafos de De Bruijn (short-reads)
- Overlap-Layout-Consensus (OLC) (long-reads)
- Contigs e Scaffolds
- K-mers
- SPAdes
- Qualidade da montagem (número de contigs, Tamanho total da montagem, N50, L50)
- Continuidade × completude × correção

---

## Atividade assíncrona - Manhã

### Leitura de artigo científico

Yahav, T., Privman, E. A comparative analysis of methods for de novo assembly of hymenopteran genomes using either haploid or diploid samples. Sci Rep 9, 6480 (2019). https://doi.org/10.1038/s41598-019-42795-6.

Após a leitura do artigo, responda às seguintes questões:

1. Qual era o objetivo do estudo?
2. Qual(is) organismo(s) foi(foram) analisado(s)?
3. Que tipo de dado foi utilizado (genoma, transcriptoma, metagenoma, proteoma etc.)?
4. Quais ferramentas de bioinformática foram utilizadas?
5. Quais bancos de dados foram utilizados?
6. Quais foram os principais resultados obtidos? Responda essa questão de acordo com as seguintes perguntas:
   - Qual das quatro montagens apresentou o maior N50 de contig? O que esse resultado indica sobre a continuidade da montagem?
   - Compare as montagens haploide e diploide realizadas com SPAdes. Qual apresentou maior N50? O que poderia explicar essa diferença?
   - O tamanho esperado do genoma de C. hyspanica era de aproximadamente 220 Mb. Qual montagem apresentou um tamanho total mais próximo desse valor?
   - A montagem diploide SPAdes apresentou maior completude pelo BUSCO (84%) do que a haploide SPAdes (68%). Isso significa necessariamente que ela é uma montagem melhor? Justifique.

---
