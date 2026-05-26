# Seleção do LLM para pré-rotulagem (cold start)

Documento suplementar à Seção 3.1 do artigo SBBD. Resume a exploração qualitativa que antecedeu a adoção do **Qwen-2.5-72B** como pré-rotulador inicial do acervo autêntico e como *baseline* generativo do estudo.

## Contexto

- **Objetivo:** escolher um LLM de pesos abertos executável **localmente** (*on-premise*), sem envio de peças processuais reais a APIs externas (sigilo e LGPD).
- **Tarefa:** pré-rotulagem estruturada de segmentos jurídicos trabalhistas com *prompt* alinhado à taxonomia em construção (32 classes no produto final; no arranque, o guia cobria sobretudo entidades estruturais e administrativas).
- **Não é:** *benchmark* exaustivo entre arquiteturas generativas; comparação quantitativa formal com F1 em *Gold Standard* completo (este veio depois, na avaliação do artigo).

## Modelos inspecionados

Todos executados localmente, com o mesmo *prompt* estruturado (`prompt_qwen_2.5_70b.md`), sobre excertos de prova:

| Modelo (família avaliada) | Escala aprox. |
|---------------------------|---------------|
| **Qwen2** (72B) — adotado no estudo como Qwen-2.5-72B | 72B |
| DeepSeek Coder V2 | 32B |
| Qwen2 Coder | 32B |
| Phi-3 (Microsoft) | — |

A comparação lado a lado está na figura [`analise_modelos.png`](analise_modelos.png) (excerto **fictício**, sem texto de processo real, para divulgação sem sigilo).

## Protocolo da exploração

1. Restringir candidatos a modelos **abertos** com inferência viável na infraestrutura institucional (GPU local, sem nuvem sobre textos reais).
2. Aplicar o mesmo *prompt* a trechos de prova.
3. Confrontar saídas com **referência manual** (rótulos em verde = aderentes; laranja = divergência taxonômica aceitável ou corrigível; cinza = rótulo fora do esquema ou entidade inexistente no texto).
4. Critérios de inspeção qualitativa:
   - aderência às diretrizes de anotação;
   - frequência de erros críticos (alucinação de entidades, número de processo incorreto, classes inventadas como *Documento Legal* ou *Cargo*);
   - qualidade de fronteiras (*spans*) e consistência JSON.

## Síntese qualitativa (excerto da figura)

No trecho ilustrado, observou-se:

- **Qwen2 (72B):** cobertura ampla de organizações, pessoas, códigos e valores; algumas trocas entre `LEGISLACAO` e referências processuais; rótulo *Cargo* fora do esquema de 32 classes.
- **DeepSeek Coder V2 (32B):** uso de rótulos genéricos (*Documento Legal*); erro em número de precatório em relação ao texto de referência.
- **Phi-3:** duplicação de identificadores e mistura de classes (`CODIGO_IDENTIFICADOR` vs referência a ato); rótulos de função fora do esquema.
- **Qwen2 Coder (32B):** alucinação de pessoa inexistente no excerto; confusão entre `LEGISLACAO` e `JURISPRUDENCIA`.

## Resultado da seleção

O **Qwen2 / Qwen-2.5-72B** foi selecionado por, na amostra inspecionada, apresentar **menor incidência de erros críticos** (alucinações e distorções factuais) e **aderência relativa mais próxima** das diretrizes frente aos demais candidatos no mesmo ambiente.

**Limitações comuns a todos os generativos:** delimitação imprecisa de *spans*, rótulos fora do esquema e dependência de pós-processamento para índices de caracteres — motivando revisão humana obrigatória no *cold start* e, depois, migração progressiva do pré-rotulador para o *encoder* especializado (BERTimbau).

## Relação com o baseline do artigo

O Qwen adotado na pré-rotulagem é o mesmo modelo reavaliado quantitativamente no artigo (F1 no *Gold Standard* de 100 segmentos, latência e VRAM na Tabela comparativa), com *prompt* otimizado e protocolo *Exact Match* descritos no manuscrito.

## Artefatos relacionados

| Arquivo | Conteúdo |
|---------|----------|
| [`analise_modelos.png`](analise_modelos.png) | Comparação visual lado a lado (excerto fictício) |
| `prompt_qwen_2.5_70b.md` | *Prompt* de pré-rotulagem |
| `diretrizes_anotacao_ner.pdf` | Manual de anotação |
