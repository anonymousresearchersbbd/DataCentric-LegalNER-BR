# DataCentric-LegalNER-BR

Este repositório contém artefatos e dados relacionados ao projeto **DataCentric-LegalNER-BR**, voltado para o Reconhecimento de Entidades Nomeadas (REN) no domínio jurídico brasileiro utilizando abordagens centradas em dados (Data-Centric).

## Conteúdo do Repositório

Neste projeto, você encontrará os seguintes artefatos principais:

- **`100_amostras_gold_standard_reformulado.jsonl`**
  Este arquivo contém 100 amostras textuais com anotações de entidades em formato JSONL.
  > ⚠️ **Aviso Importante**: Este *gold standard* de 100 exemplares foi **reformulado (anonimizado e modificado)** exclusivamente para fins de disponibilização pública e demonstração da estrutura. **Ele NÃO é o mesmo conjunto de dados utilizado nos testes reais e oficiais do projeto**, garantindo assim a privacidade e a segurança de dados sensíveis presentes nos processos originais.

- **`classes_entidades.md`**
  Detalhamento em formato de tabela de todas as classes de entidades mapeadas no projeto. Inclui descrições, exemplos práticos de formatação, frequência de ocorrência no dataset e métricas de F1-Score obtidas.

- **`Documento Base de Definições de Padrões de Texto.pdf`**
  Documentação de referência que estabelece as diretrizes completas, regras de negócio e padrões utilizados para a anotação e extração das entidades no escopo jurídico.

- **`distribuicao_categorias_amostras_gold_standard_real.jsonl`**
  Apresenta a distribuição quantitativa e o percentual das categorias de entidades nomeadas mapeadas no corpus do *gold standard* real. (Nota: o conteúdo do arquivo está estruturado em formatação texto/markdown).

- **`modelo_dados.json`**
  Arquivo que define a estrutura de dados e o formato JSON estrito esperado para as saídas geradas pelo modelo de NER durante a extração.

- **`prompt_qwen_2.5_70b.md`**
  O *prompt* otimizado e detalhado utilizado no modelo LLM `Qwen/Qwen2.5-70B-Instruct` para a tarefa de pré-anotação estruturada (*pre-labeling*) de documentos jurídicos.
