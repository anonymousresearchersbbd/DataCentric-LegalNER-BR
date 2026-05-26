# Geração sintética do corpus (cold start)

Documento suplementar à Seção 3.1 do artigo SBBD. Descreve a geração de exemplos **fictícios** para mitigar *cold start* e desbalanceamento de classes, sem envio de peças processuais reais a APIs externas.

## Modelo e governança

| Aspecto | Configuração |
|---------|----------------|
| **Modelo** | Gemini 2.5 Flash (API em nuvem) |
| **Escopo** | Única etapa do projeto com chamada a API externa |
| **Entrada** | Instruções de taxonomia, parâmetros de lote e **cenários inventados** — nunca documentos do PJe/PROAD |
| **Saída** | Lista JSON de trechos com entidades; normalização posterior ao *schema* de 32 classes e registro em `modelo_dados.json` (`proveniencia.tipo`: `lgpd` ou complementar; `versao_prompt`, p.ex. v5.0) |
| **Volume no corpus final (artigo)** | 15.877 segmentos sintéticos (~29,8% do treino); 68,4% da massa sintética com auditoria humana direta |

## Duas trilhas de *prompt*

### 1. Entidades administrativas e estruturadas

**Arquivo:** [`prompt_sintetico_entidades.md`](prompt_sintetico_entidades.md)

**Função:** gerar, em lote, textos fictícios de fragmentos trabalhistas com entidades de rotina processual (pessoas, organizações, CPF/CNPJ, endereços, legislação, valores, datas, etc.).

**Controles de diversidade (placeholders por lote):**

- `{quantidade_exemplares}` — tamanho do lote
- `{foco}` — tema do lote
- `{estilo}` — registro textual
- `{complexidade}` — complexidade sintática

**Regras de qualidade no *prompt*:**

- Textos entre **150 e 200 palavras**; densidade de entidades variável (1–3 a 10–15 por exemplar)
- **~10%** de exemplos negativos (menção a entidade sem valor literal anotável)
- Simulação de *chunks* reais: início/fim abruptos (~40\% cada) e parágrafos completos (~20\%)
- Nomes e organizações fictícios, sem repetição sistemática
- *Schema* fechado de rótulos no gerador; saída **somente JSON** (sem markdown)
- Regras de anotação: endereço unificado, legislação não fragmentada, prefixos incluídos no *span*, distinção Organização vs.\ Local

**Papel no corpus:** reforço do bloco **sintético complementar** (classes raras fora do núcleo LGPD e cobertura estrutural).

---

### 2. Categorias sensíveis (LGPD)

**Arquivo:** [`prompt_sintetico_lgpd.md`](prompt_sintetico_lgpd.md)

**Função:** identificar entidades sensíveis em textos já disponíveis (trecho unitário ou lote), complementando anotações prévias — **não repetir** entidades já marcadas.

**Modos:**

| Modo | Uso |
|------|-----|
| `DETECAO_ENTIDADES` | Um documento: `{texto}` + `{entidades_marcadas_texto}` |
| `DETECAO_ENTIDADES_LOTE` | Vários documentos: `{documentos_combinados}`, `{quantidade_documentos}` |

**Rótulos no *prompt* (6 categorias fechadas):**

`ORIGEM_RACIAL_ETNICA`, `CONVICCAO_RELIGIOSA`, `OPINIAO_POLITICA`, `FILIACAO_ORGANIZACIONAL`, `DADO_SAUDE_VIDA_SEXUAL`, `DADO_GENETICO_BIOMETRICO`

Na taxonomia final de 32 classes do artigo, `FILIACAO_ORGANIZACIONAL` e `DADO_GENETICO_BIOMETRICO` são normalizadas ou mapeadas conforme as diretrizes de anotação durante a curadoria.

**Papel no corpus:** reforço do bloco **`tipo=lgpd`** (categorias do art. 11 e afins), com **auditoria humana prioritária** antes da incorporação definitiva.

---

## Filtros e pós-processamento (pipeline)

1. **Validação estrutural:** resposta estritamente JSON; rejeição de saídas com markdown ou rótulos fora do *schema* do *prompt*.
2. **Normalização taxonômica:** mapeamento dos rótulos do gerador para as 32 classes do manual (`diretrizes_anotacao_ner.pdf`) e cálculo de índices de caracteres no padrão do corpus.
3. **Segmentação:** alinhamento ao mesmo fluxo de *chunks* (NLTK, limite de 512 *tokens*) dos dados reais.
4. **Deduplicação e descarte:** remoção de duplicatas textuais grosseiras e de exemplos inválidos antes da fila de revisão.
5. **Curadoria humana:** revisão obrigatória concentrada na massa sintética (68,4\% revisados no total reportado no artigo), com ênfase em categorias LGPD para evitar estereótipos e padrões identificáveis.

## Mitigação de artefatos sintéticos

- Cenários **100\% fictícios** na chamada externa; textos reais só em processamento local (pré-rotulagem Qwen, *encoder*).
- Diversidade explícita por lote (foco, estilo, complexidade) e regras anti-padronização (nomes não repetidos, *chunks* abruptos).
- Viés de padronização linguística reconhecido na Discussão; mitigado por ciclos posteriores com dados reais e aprendizado ativo.

## Artefatos relacionados

| Arquivo | Conteúdo |
|---------|----------|
| `prompt_sintetico_entidades.md` | *Prompt* de geração — entidades administrativas/estruturadas |
| `prompt_sintetico_lgpd.md` | *Prompt* de detecção — categorias sensíveis (unitário e lote) |
| `modelo_dados.json` | Exemplo de registro com `proveniencia.tipo=lgpd` |
| `diretrizes_anotacao_ner.pdf` | Taxonomia final de 32 classes |
