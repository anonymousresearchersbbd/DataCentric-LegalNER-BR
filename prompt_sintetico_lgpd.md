## DETECAO_ENTIDADES (prompt unitário)
Você é um especialista em identificação de dados sensíveis conforme a LGPD.

TAREFA: Analise o texto abaixo e identifique TODAS as entidades sensíveis que NÃO foram marcadas anteriormente.

TEXTO PARA ANÁLISE:
"{texto}"

ENTIDADES JÁ MARCADAS (NÃO REPITA):
{entidades_marcadas_texto}

# SCHEMA FECHADO — USE APENAS ESTES 6 RÓTULOS NO CAMPO "label"
- ORIGEM_RACIAL_ETNICA: raça, cor, ascendência ou origem nacional/étnica
  Exemplos: branca, parda, preta, negra, indígena, amarela, quilombola, cigana, homem negro, descendente de japoneses
- CONVICCAO_RELIGIOSA: crença, fé ou ausência dela
  Exemplos: católico praticante, umbandista, evangélico da Assembleia de Deus, espírita kardecista, judeu ortodoxo, muçulmano, ateu convicto
- OPINIAO_POLITICA: posicionamentos políticos (NÃO filiação formal a partido/sindicato)
  Exemplos: militante do movimento estudantil, participante de manifestação pela democracia, apoiador declarado de candidato, defensor do liberalismo, crítico do governo em redes sociais, intenção de voto declarada
- FILIACAO_ORGANIZACIONAL: associação FORMAL a sindicato, partido político ou organização religiosa/filosófica
  Exemplos: filiado ao PT, membro do PL, dirigente do Sindicato dos Metalúrgicos, associado ao Movimento Brasil Livre (MBL), membro da maçonaria, dizimista na Igreja Universal
- DADO_SAUDE_VIDA_SEXUAL: saúde física/mental, orientação sexual, identidade de gênero
  Exemplos: portador de HIV, diagnóstico de câncer, transtorno bipolar, depressão crônica, esquizofrenia, epilepsia, laudo de invalidez, atestado de doença grave, tratamento psiquiátrico
- DADO_GENETICO_BIOMETRICO: dados genéticos ou biométricos vinculados a pessoa identificável
  Exemplos: exame de DNA, mapeamento genético, sequenciamento de genoma, reconhecimento facial, impressão digital, padrão de voz, geometria da mão

INSTRUÇÕES:
1. Identifique APENAS entidades que NÃO foram marcadas anteriormente
2. Use SOMENTE um dos 6 rótulos acima — nenhum outro label é permitido
3. Seja minucioso — procure menções diretas e indiretas
4. Inclua variações linguísticas, sinônimos, expressões regionais
5. O campo "text" deve ser o trecho exato copiado do texto analisado
6. Retorne APENAS o JSON com as entidades encontradas
7. Se não encontrar nenhuma entidade nova, retorne []

FORMATO DE RESPOSTA (JSON):
[
  {"text": "segmento_exato_do_texto", "label": "ORIGEM_RACIAL_ETNICA"},
  {"text": "outro_segmento", "label": "CONVICCAO_RELIGIOSA"}
]

---

## DETECAO_ENTIDADES_LOTE (prompt em lote)
Você é um especialista em identificação de dados sensíveis conforme a LGPD.

TAREFA: Analise os {quantidade_documentos} documentos abaixo e identifique TODAS as entidades sensíveis que NÃO foram marcadas anteriormente em cada um.

DOCUMENTOS PARA ANÁLISE:
{documentos_combinados}

# SCHEMA FECHADO — USE APENAS ESTES 6 RÓTULOS NO CAMPO "label"
- ORIGEM_RACIAL_ETNICA: raça, cor, ascendência ou origem nacional/étnica
- CONVICCAO_RELIGIOSA: crença, fé ou ausência dela
- OPINIAO_POLITICA: posicionamentos políticos (NÃO filiação formal)
- FILIACAO_ORGANIZACIONAL: associação FORMAL a sindicato, partido ou organização religiosa/filosófica
- DADO_SAUDE_VIDA_SEXUAL: saúde física/mental, orientação sexual, identidade de gênero
- DADO_GENETICO_BIOMETRICO: dados genéticos ou biométricos vinculados a pessoa identificável

INSTRUÇÕES:
1. Para CADA documento, identifique APENAS entidades que NÃO foram marcadas anteriormente
2. Use SOMENTE um dos 6 rótulos acima — nenhum outro label é permitido
3. Seja minucioso — procure menções diretas e indiretas
4. Inclua variações linguísticas, sinônimos, expressões regionais
5. Retorne APENAS o JSON com as entidades encontradas para cada documento
6. Se um documento não tiver entidades novas, retorne lista vazia para ele

FORMATO DE RESPOSTA (JSON):
{
  "documento_1": [
    {"text": "segmento_exato_do_texto", "label": "DADO_SAUDE_VIDA_SEXUAL"}
  ],
  "documento_2": [],
  "documento_3": [
    {"text": "entidade_encontrada", "label": "FILIACAO_ORGANIZACIONAL"}
  ]
}
