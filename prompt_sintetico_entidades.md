Você é um especialista sênior na criação de dados sintéticos para NER em documentos do TRIBUNAL DO TRABALHO brasileiro.

TAREFA: Gerar uma lista JSON contendo {quantidade_exemplares} textos sintéticos com entidades para treinar um modelo de NER.

# --- DIRETRIZES DE GERAÇÃO PARA ESTE LOTE ---
- FOCO TEMÁTICO: {foco}.
- ESTILO DE ESCRITA: {estilo}.
- COMPLEXIDADE DO TEXTO: {complexidade}.

# --- SCHEMA FECHADO DE RÓTULOS (USE APENAS ESTES NO CAMPO "label") ---

## Pessoas e organizações
- Pessoa
- Organização
- PCE

## Locais e endereços
- Local
- CEP
- Número da Casa

## Documentos e códigos
- CPF
- CNPJ
- Identidade
- CTPS
- PIS
- OAB
- Matrícula
- Número Interno

## Financeiro e numérico
- Dinheiro
- Porcentagem
- Dados Bancários
- Números

## Tempo e comunicação
- Data
- Hora
- Telefone
- E-mail
- URL
- IP

## Jurídico
- Legislação
- Jurisprudência

## Outros (prioritários no lote)
- Estado Civil

**Regra:** Não invente rótulos fora desta lista. O valor de "label" deve coincidir exatamente com um dos nomes acima (incluindo acentuação e maiúsculas).

# --- FORMATO DE SAÍDA OBRIGATÓRIO ---
- Retorne APENAS uma lista de objetos JSON válida.
- Não inclua markdown (```json), explicações ou qualquer texto fora da lista JSON.
- Estrutura de cada objeto:
  {
    "text": "...",
    "entities": [
      {"text": "entidade 1", "label": "CPF"},
      {"text": "entidade 2", "label": "Pessoa"}
    ]
  }

# --- REGRAS CRÍTICAS DE QUALIDADE E DIVERSIDADE ---
1. **TAMANHO E CONCISÃO:** Mantenha os textos concisos e realistas, como fragmentos de documentos. **O tamanho ideal é entre 150 e 200 palavras.** Evite textos excessivamente longos.
2. **DENSIDADE DE ENTIDADES VARIÁVEL:** Gere uma mistura de exemplos: alguns com muitas entidades (10-15), outros com poucas (1-3).
3. **EXEMPLOS NEGATIVOS (IMPORTANTE):** Ocasionalmente (cerca de 10% das vezes), gere textos que mencionam a existência de uma entidade (ex: "o CPF do autor", "o endereço da empresa") mas SEM fornecer o dado em si. Nesses casos, a lista "entities" deve estar vazia ou não conter essa entidade específica.
4. **NOMES E ORGANIZAÇÕES FICTÍCIOS:** Crie nomes de pessoas, empresas, escritórios e sindicatos que sejam criativos, realistas e brasileiros. EVITE REPETIR OS MESMOS NOMES.
5. **CONSTRUÇÃO DE TEXTO (CHUNKS):** Simule fragmentos de documentos reais:
   - **Início Abrupto (~40%):** Comece com letra minúscula e um conectivo (ex: "sendo assim...", "conforme o art..."). NUNCA use reticências no início.
   - **Fim Abrupto (~40%):** Termine a frase de forma inconclusiva (ex: "conforme documentação anexa, e...", "devendo ser observado o prazo de...").
   - **Texto Completo (~20%):** Parágrafos coesos com início e fim bem definidos.

# --- REGRAS DE ANOTAÇÃO DE ENTIDADES (SEGUIR RIGOROSAMENTE) ---
6. **ENTIDADES PRIORITÁRIAS:** Cada exemplar deve tentar incluir pelo menos uma destas: Dados Bancários, Identidade, PIS, IP, Estado Civil, URL.
7. **COBERTURA DO LOTE:** Distribua também CPF, CNPJ, Telefone, Porcentagem, CEP, E-mail, Hora, Número Interno, Dinheiro, PCE, Local, Data, Organização, Pessoa, Legislação, Números, Matrícula, Jurisprudência, CTPS, OAB.
8. **AGRUPAMENTO DE ENDEREÇOS:** Endereços com rua e bairro devem ser UMA SÓ entidade Local. Ex: "Rua das Flores, 123, bairro Jardim".
9. **LEGISLAÇÃO COMPLETA:** NUNCA fragmente artigos de lei. "art. 75, inciso II, da Lei nº 14.133/2021" é UMA SÓ entidade Legislação.
10. **INCLUSÃO DE PREFIXOS:** Capture o prefixo junto com o valor.
    - "CPF: 123.456.789-00" -> CPF
    - "Matrícula 12345" -> Matrícula
    - "RG 12.345.678-X" -> Identidade
    - "Telefone: (11) 98765-4321" -> Telefone
    - "OFÍCIO Nº 123/2024" -> Número Interno
11. **DISTINÇÃO DE NÚMEROS:**
    - Use Números APENAS para valores numéricos isolados (ex: "5" em "5 dias úteis").
    - NÃO use para ordinais em nomes (ex: "8ª" em "8ª Vara do Trabalho").
    - NÃO use para valores monetários (Dinheiro) ou percentuais (Porcentagem).
12. **ORGANIZAÇÃO vs. LOCAL:**
    - "TRIBUNAL REGIONAL DO TRABALHO DA 13ª REGIÃO" -> Organização.
    - "3ª VARA DO TRABALHO DE BELÉM/PA" -> Organização. (O local faz parte do nome da organização).
    - Use Local para endereços físicos e cidades/estados isolados quando não fazem parte do nome formal de uma organização.

Agora, gere a lista JSON seguindo todas essas regras, com foco especial na qualidade e diversidade.
