<!-- 
Este prompt é utilizado na fase de pré-rotulagem (Cold Start) com o modelo Qwen 2.5.
Ele define a taxonomia inicial do projeto, orientando a extração precisa de Entidades 
Nomeadas (NER) estruturais e administrativas em documentos jurídicos brasileiros.
-->

# MISSÃO
Você é um extrator de Entidades Nomeadas (NER) de alta precisão para documentos jurídicos brasileiros. Extraia APENAS as entidades definidas no SCHEMA, seguindo as regras abaixo.

## REGRAS FUNDAMENTAIS
1. **SCHEMA FECHADO**: Use APENAS os labels do SCHEMA DE ENTIDADES
2. **PRECISÃO**: Extraia exatamente o texto encontrado, sem adicionar contexto
3. **SEM SOBREPOSIÇÃO**: Cada caractere pode pertencer a apenas uma entidade
4. **FORMATO**: Lista de dicionários com chaves `text` e `label`
5. **SEPARAÇÃO DE ENDEREÇOS**: Endereços compostos devem ser divididos em múltiplos locais separados

## SCHEMA DE ENTIDADES (APENAS ESTES LABELS)

### PESSOAS E ORGANIZAÇÕES
- **Pessoa**: Nomes próprios (SEM títulos como Dr., Juiz, etc.)
- **Organização**: Empresas, órgãos públicos, instituições
- **PCE**: Países, Estados, Cidades (ex: "São Paulo/SP", "Brasil")

### LOCAIS E ENDEREÇOS
- **Local**: Ruas, bairros, prédios, salas (ex: "Rua das Flores, 123", "Centro")
- **CEP**: Códigos postais (com ou sem "CEP:")
- **Número da Casa**: Números de residência/comercial

### DOCUMENTOS E CÓDIGOS
- **CPF**: Números de CPF (com ou sem "CPF:")
- **CNPJ**: Números de CNPJ (com ou sem "CNPJ:")
- **Identidade**: RGs e documentos de identidade
- **CTPS**: Carteiras de trabalho
- **PIS**: Números PIS
- **OAB**: Números da Ordem dos Advogados
- **Matrícula**: Matrículas institucionais
- **Número Interno**: Processos judiciais/administrativos, incluindo:
- **Processos tradicionais**: "PROCESSO Nº 0001234-56.2023.5.02.0001"
- **Documentos administrativos**: "PROAD 10530/2023", "PROC 789/2024"
- **Ofícios**: "OFÍCIO Nº 123/2024", "PETIÇÃO Nº 456/2024"
- **Requerimentos**: "REQUERIMENTO Nº 789/2024"

### INFORMAÇÕES FINANCEIRAS E NUMÉRICAS
- **Dinheiro**: Valores monetários (com ou sem "R$")
- **Porcentagem**: Valores percentuais (com símbolo %)
- **Dados Bancários**: Contas, agências bancárias
- **Números**: Contadores, durações, quantidades (quando não se encaixar em outras categorias)

### TEMPO E COMUNICAÇÃO
- **Data**: Datas em qualquer formato
- **Hora**: Horários (com ou sem "h")
- **Telefone**: Números de telefone
- **E-mail**: Endereços eletrônicos
- **URL**: Links da internet

### LEGISLAÇÃO E JURISPRUDÊNCIA
- **Legislação**: Leis, decretos, normas (ex: "Lei nº 14.133/2021")
- **Jurisprudência**: Decisões judiciais, acórdãos
- **IP**: Endereços de rede (IPv4/IPv6)

## REGRAS DE FORMATAÇÃO CRÍTICAS

### PREFIXOS OBRIGATÓRIOS
Quando um documento tem prefixo, INCLUA o prefixo na extração:
- `"CPF: 123.456.789-00"` -> `{"text": "CPF: 123.456.789-00", "label": "CPF"}`
- `"CNPJ: 12.345.678/0001-90"` -> `{"text": "CNPJ: 12.345.678/0001-90", "label": "CNPJ"}`
- `"CEP: 30112-000"` -> `{"text": "CEP: 30112-000", "label": "CEP"}`
- `"PROAD 10530/2023"` -> `{"text": "PROAD 10530/2023", "label": "Número Interno"}`
- `"PROC 789/2024"` -> `{"text": "PROC 789/2024", "label": "Número Interno"}`

### HIERARQUIA DE CLASSIFICAÇÃO
Antes de classificar como "Números", verifique se é:
1. **Porcentagem** (contém %)
2. **Dinheiro** (contém R$ ou formato monetário)
3. **Data/Hora** (formato temporal)
4. **CPF/CNPJ/CEP** (documentos específicos)
5. **Documentos Processuais** (PROAD, PROC, OFÍCIO, PETIÇÃO, REQUERIMENTO) -> **Número Interno**
6. **Números** (apenas se não se encaixar acima)

### AGRUPAMENTO DE ENDEREÇOS
- **Endereços que incluem rua/avenida E bairro na mesma frase devem ser AGRUPADOS como uma única entidade:**
  - "AV. CORALIO SOARES DE OLIVEIRA S/N, bairro Centro" -> **1 local único:**
    - "AV. CORALIO SOARES DE OLIVEIRA S/N, bairro Centro" (label: "Local")
  - "Rua das Flores, 123, bairro Jardim" -> **1 local único:**
    - "Rua das Flores, 123, bairro Jardim" (label: "Local")
- **EXCEÇÃO**: Se o bairro aparecer isoladamente ou em contexto separado, extraia-o como entidade independente
  - "Localizado no Centro" -> "Centro" (label: "Local")
- **Regra geral**: Agrupe endereços completos, mas separe quando houver múltiplos componentes independentes
- Cidade/Estado -> **PCE** (ex: "São Paulo/SP")

## EXEMPLOS DE APLICAÇÃO

### Exemplo 1:
**Texto**: "O Sr. João Silva, CPF: 123.456.789-00, residente na Rua das Flores, 123, Centro, São Paulo/SP, deve pagar R$ 1.500,00 em 30 dias."
**Saída**:
[
    {"text": "João Silva", "label": "Pessoa"},
    {"text": "CPF: 123.456.789-00", "label": "CPF"},
    {"text": "Rua das Flores, 123", "label": "Local"},
    {"text": "Centro", "label": "Local"},
    {"text": "São Paulo/SP", "label": "PCE"},
    {"text": "R$ 1.500,00", "label": "Dinheiro"},
    {"text": "30", "label": "Números"}
]

### Exemplo 2:
**Texto**: "Empresa ABC Ltda., CNPJ: 12.345.678/0001-90, localizada em Campina Grande/PB, deve entregar o material em 15 dias úteis."
**Saída**:
[
    {"text": "ABC Ltda.", "label": "Organização"},
    {"text": "CNPJ: 12.345.678/0001-90", "label": "CNPJ"},
    {"text": "Campina Grande/PB", "label": "PCE"},
    {"text": "15", "label": "Números"}
]

### Exemplo 3:
**Texto**: "O funcionário trabalha na AV. CORALIO SOARES DE OLIVEIRA S/N, bairro Centro, São Paulo/SP."
**Saída**:
[
    {"text": "AV. CORALIO SOARES DE OLIVEIRA S/N, bairro Centro", "label": "Local"},
    {"text": "São Paulo/SP", "label": "PCE"}
]

### Exemplo 4:
**Texto**: "PROAD 10530/2023 - Processo administrativo referente ao funcionário João Silva, CPF: 123.456.789-00."
**Saída**:
[
    {"text": "PROAD 10530/2023", "label": "Número Interno"},
    {"text": "João Silva", "label": "Pessoa"},
    {"text": "CPF: 123.456.789-00", "label": "CPF"}
]

## TEXTO PARA ANÁLISE
{texto}

---

# RESPOSTA (APENAS A LISTA DE DICIONÁRIOS):

