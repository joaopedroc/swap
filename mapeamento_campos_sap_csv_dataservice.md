# Mapeamento de Campos - SAP, CSV Databricks e DataService

Este documento mapeia os campos utilizados no ETL de Manutenções, mostrando a correspondência entre as tabelas SAP (origem), os arquivos CSV extraídos do Databricks (intermediário) e os campos enviados ao DataService (destino).

---

## 1. NOTAS DE MANUTENÇÃO (Maintenance Notifications)

### 1.1 Tabela Principal de Notas

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `nota_sap` | `QMEL` | `QMNUM` | `notas.csv` | `nota_sap` | Número da notificação de manutenção |
| `categoria_nota` | `QMEL` | - | `notas.csv` | `categoria_nota` | Categoria da notificação |
| `descricao` | `QMEL` | `QMTXT` | `notas.csv` | `descricao` | Descrição da notificação |
| `ordem` | `QMEL` | `AUFNR` | `notas.csv` | `ordem` | Ordem de serviço vinculada |
| `local_instalacao` | `QMFE` | `TPLNR` | `notas.csv` | `local_instalacao` | Local de instalação funcional |
| `subsistema` | `QMFE` / `ILOA` | - | `notas.csv` | `subsistema` | Subsistema do equipamento |
| `numero_serie` | `QMFE` | `EQUNR` | `notas.csv` | `numero_serie` | Número do equipamento |
| `tipo` | `QMEL` | - | `notas.csv` | `tipo` | Tipo da nota |
| `tipo_desc` | Tabela auxiliar | - | - | - | Descrição do tipo (via aux_tables.xlsx) |
| `possui_texto_descritivo` | `QMEL` | - | `notas.csv` | `possui_texto_descritivo` | Flag X se possui texto |
| `grupo_planejamento` | `QMEL` | `INGRP` | `notas.csv` | `grupo_planejamento` | Grupo de planejamento |
| `centro_trabalho` | `QMEL` | `ARBPL` | `notas.csv` | `centro_trabalho` | Centro de trabalho |
| `centro_trabalho_desc` | Tabela auxiliar | - | - | - | Descrição do centro (via aux_tables.xlsx) |
| `notificador` | `QMEL` | `QMNAM` | `notas.csv` | `notificador` | Quem criou a notificação |
| `data_nota` | `QMEL` | `QMDAT` + `MZEIT` | `notas.csv` | `dt_data_nota` + `hr_data_nota` | Data e hora da notificação |
| `inicio_desejado` | `QMEL` | `STRDT` + `STRUR` | `notas.csv` | `dt_inicio_desejado` + `hr_inicio_desejado` | Início desejado |
| `conclusao_desejada` | `QMEL` | `LTRMN` + `LTRUR` | `notas.csv` | `dt_conclusao_desejada` + `hr_conclusao_desejada` | Conclusão desejada |
| `teve_parada_sap` | `QMEL` | - | `notas.csv` | `teve_parada_sap` | Flag de parada |
| `duracao_parada_em_horas` | `QMEL` | `AUSVN` | `notas.csv` | `duracao_parada_em_horas` | Duração da parada (segundos → horas) |
| `duracao_ordem_em_horas` | Calculado | - | `data_real_ordem.csv` | - | Calculado: (data_real_fim - data_real_inicio) |
| `prioridade` | `QMEL` | `PRIOK` | `notas.csv` | `prioridade` | Prioridade da notificação |
| `inicio_avaria` | `QMEL` | `AUZTD` + `AUZTC` | `notas.csv` | `dt_inicio_avaria` + `hr_inicio_avaria` | Início da avaria |
| `fim_avaria` | `QMEL` | `AUZTB` + `AUZTA` | `notas.csv` | `dt_fim_avaria` + `hr_fim_avaria` | Fim da avaria |
| `encerramento_por_data` | `QMEL` | - | `notas.csv` | `dt_encerramento_por_data` + `hr_encerramento_por_data` | Data de encerramento |
| `possui_medidas` | `QMEL` | - | `notas.csv` | `possui_medidas` | Flag X se possui medidas |
| `componente` | `QMEL` | - | `notas.csv` | `componente` | Componente |
| `data_ref_etl` | - | - | `notas.csv` | `dt_data_ref_etl` | Data de referência do ETL |
| `sistema` | Derivado | - | - | - | Extraído do local_instalacao |
| `uhe` | Derivado | - | - | - | Extraído do local_instalacao (via MANAGEMENT_UHE_DICT) |

### 1.2 Status de Notas

#### Status de Sistema

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `status_sistema` | `JEST` | `STAT` | `status_sys.csv` / `status_sys_hana.csv` | `status_sistema_short` | Status do sistema (agregado com hierarquia) |

**Arquivo CSV:** `status_sys.csv` / `status_sys_hana.csv`
- Colunas: `nota_sap`, `status_sistema_short`
- Hierarquia aplicada: `NOTIF_SYS_STATUS_HIERARCHY` (EREI, MEAB, MREL, MSEN, MSIM, MSPN, MSPR, ORDA, TMEE)

#### Status de Usuário

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `status_usuario` | `JEST` / `TJ30T` | `STAT` + `TXT04` | `status_user.csv` / `status_user_hana.csv` | `status_usuario_short` | Status do usuário (agregado com hierarquia) |
| `status_usr_desc` | Tabela auxiliar | - | - | - | Descrição do status (via aux_tables.xlsx) |

**Arquivo CSV:** `status_user.csv` / `status_user_hana.csv`
- Colunas: `nota_sap`, `status_usuario_short`
- Hierarquia aplicada: `NOTIF_USER_STATUS_HIERARCHY` (PRGR, ENCE, EMAN, SUPR, CANC, EXEC, ESGO)

### 1.3 Sintoma e Causa

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `sintoma` | `QMFE` | `FECOD` | `sintoma_causa.csv` / `sintoma_causa_hana.csv` | `cod_sintoma` | Código do sintoma |
| `causa` | `QMUR` | `URCOD` | `sintoma_causa.csv` / `sintoma_causa_hana.csv` | `causa` | Código da causa |
| `causa_texto` | Tabela auxiliar | - | - | - | Texto da causa (via aux_tables.xlsx) |

**Arquivo CSV:** `sintoma_causa.csv` / `sintoma_causa_hana.csv`
- Colunas: `QMNUM` (nota_sap), `cod_sintoma`, `causa`, `FENUM`

### 1.4 Dados de Instalação

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `data_instalacao` | `EQUI` | `DATAB` | `data_inst.csv` / `data_inst_hana.csv` | `dt_data_instalacao` | Data de instalação do equipamento |

**Arquivo CSV:** `data_inst.csv` / `data_inst_hana.csv`
- Colunas: `numero_serie`, `dt_data_instalacao`

### 1.5 Dados Reais de Ordem (vinculados à Nota)

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `duracao_ordem_em_horas` | `AFRU` | `ISDD` + `IEDD` | `data_real_ordem.csv` / `data_real_ordem_hana.csv` | `dt_data_real_inicio`, `hr_data_real_inicio`, `dt_data_real_fim`, `hr_data_real_fim` | Calculado como diferença em horas |

**Arquivo CSV:** `data_real_ordem.csv` / `data_real_ordem_hana.csv`
- Colunas: `nota_sap`, `dt_data_real_inicio`, `hr_data_real_inicio`, `dt_data_real_fim`, `hr_data_real_fim`

### 1.6 Dados Complementares

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `nome1` | `IFLOT` / `EQUI` | `PLTXT` / `EQKTX` | `nome1.csv` | `nome1` | Nome do local de instalação |

**Arquivo CSV:** `nome1.csv`
- Colunas: `local_instalacao`, `nome1`

---

## 2. ORDENS DE SERVIÇO (Service Orders)

### 2.1 Tabela Principal de Ordens

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `ordem` | `AFIH` / `AUFK` | `AUFNR` | `ordens.csv` | `ordem` | Número da ordem de manutenção |
| `descricao` | `AUFK` | `KTEXT` | `ordens.csv` | `descricao` | Descrição da ordem |
| `data_ordem` | `AUFK` | `ERDAT` + `ERZEIT` | `ordens.csv` | `dt_data_ordem` + `hr_data_ordem` | Data e hora de criação da ordem |
| `tipo_ordem` | `AUFK` | `AUART` | `ordens.csv` | `tipo_ordem` | Tipo da ordem (GAA, GRA, etc.) |
| `nota_sap` | `AFIH` | `QMNUM` | `ordens.csv` | `nota_sap` | Notificação vinculada à ordem |
| `tipo_atividade` | `AUFK` | `ILART` | `ordens.csv` | `tipo_atividade` | Tipo de atividade |
| `data_base_inicio` | `AUFK` | `GSTRP` | `ordens.csv` | `dt_data_base_inicio` | Data base de início |
| `data_base_fim` | `AUFK` | `GLTRP` | `ordens.csv` | `dt_data_base_fim` | Data base de fim |
| `sistema` | `AFIH` / `ILOA` | - | `ordens.csv` | `sistema` | Sistema do equipamento |
| `subsistema` | `AFIH` / `ILOA` | - | `ordens.csv` | `subsistema` | Subsistema do equipamento |
| `local_instalacao` | `AFIH` / `AFVC` | `TPLNR` | `ordens.csv` | `local_instalacao` | Local de instalação funcional |
| `numero_serie` | `AFIH` / `AFVC` | `EQUNR` | `ordens.csv` | `numero_serie` | Número do equipamento |
| `grupo_planejamento` | `AUFK` | `INGRP` | `ordens.csv` | `grupo_planejamento` | Grupo de planejamento |
| `data_ref_etl` | - | - | `ordens.csv` | `dt_data_ref_etl` | Data de referência do ETL |
| `uhe` | Derivado | - | - | - | Extraído do local_instalacao (via UHE_DICT) |
| `prioridade` | `QMEL` | `PRIOK` | Derivado de `notas.csv` | - | Prioridade obtida da notificação vinculada |
| `centro_trabalho` | `QMEL` | `ARBPL` | Derivado de `notas.csv` | - | Centro de trabalho da notificação vinculada |
| `maintenance_nota_sap` | - | - | - | - | Array de notas vinculadas (campo `nota_sap`) |

**Arquivo CSV:** `ordens.csv`

### 2.2 Status de Ordens

#### Status de Sistema

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `status_sistema` | `JEST` | `STAT` | `ordens_status_sys.csv` | `status_sistema_short` | Status do sistema (agregado com hierarquia) |

**Arquivo CSV:** `ordens_status_sys.csv`
- Colunas: `ordem`, `status_sistema_short`
- Hierarquia aplicada: `ORDER_SYS_STATUS_HIERARCHY` (ABER, LIB, ENTE, ENCE, etc.)

#### Status de Usuário

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `status_usuario` | `JEST` / `TJ30T` | `STAT` + `TXT04` | `ordens_status_user.csv` | `status_usuario_short` | Status do usuário (agregado com hierarquia) |

**Arquivo CSV:** `ordens_status_user.csv`
- Colunas: `ordem`, `status_usuario_short`
- Hierarquia aplicada: `ORDER_USER_STATUS_HIERARCHY` (BAIX, JPAR, UNIT, CANC, etc.)

### 2.3 Custos

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `custos_planejados` | `COEP` | Soma de valores onde `WRTTP=1` | `ordens_custos.csv` | `WRTTP=1` + colunas de valores | Soma de colunas de valores quando WRTTP=1 |
| `custos_totais` | `COEP` | Soma de valores onde `WRTTP=4` | `ordens_custos.csv` | `WRTTP=4` + colunas de valores | Soma de colunas de valores quando WRTTP=4 |

**Arquivo CSV:** `ordens_custos.csv`
- Colunas: `OBJNR` (ordem com prefixo OR), `WRTTP`, colunas de valores monetários
- WRTTP: 1 = Planejado, 4 = Real/Total

### 2.4 Operações

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `operacao` | `AFVC` | Várias colunas | `ordens_operacao.csv` | Múltiplas colunas | Array de operações vinculadas à ordem |

**Arquivo CSV:** `ordens_operacao.csv`
- Colunas: `ordem`, `num_operacao`, demais campos da operação
- Agregado como lista por ordem, ordenado por `num_operacao` descendente

---

## 3. ATIVOS (Assets)

Os dados de ativos são derivados principalmente das tabelas de equipamentos e locais de instalação do SAP, combinados com informações das notas e ordens.

### 3.1 Campos Principais

| Campo DataService | Tabela SAP | Coluna SAP | Arquivo CSV (Databricks) | Coluna CSV | Notas |
|---|---|---|---|---|---|
| `local_instalacao` | `IFLOT` | `TPLNR` | `notas.csv` / `ordens.csv` | `local_instalacao` | Local de instalação funcional |
| `numero_serie` | `EQUI` | `EQUNR` | `notas.csv` / `ordens.csv` | `numero_serie` | Número do equipamento |
| `sistema` | Derivado de `local_instalacao` | - | - | - | Extraído do código do local |
| `subsistema` | Derivado de `local_instalacao` | - | - | - | Extraído do código do local |
| `data_instalacao` | `EQUI` | `DATAB` | `data_inst.csv` | `dt_data_instalacao` | Data de instalação |
| `tree_sn_tag` | - | - | `asset_tree.csv` | - | Árvore hierárquica de ativos |
| `ultima_manutencao_corretiva` | Calculado | - | - | - | Última data de nota tipo corretiva |
| `ultima_manutencao_preventiva` | Calculado | - | - | - | Última data de nota tipo preventiva |
| `frequencia_manutencao_preventiva` | Calculado | - | - | - | Frequência calculada de manutenções |

**Arquivo CSV adicional:** `asset_tree.csv`
- Estrutura hierárquica de ativos para construção da árvore

---

## 4. RELATÓRIOS DE EVENTOS (Event Reports)

Os relatórios de eventos são obtidos de fontes externas (Pysis) e não diretamente do SAP.

### 4.1 Campos

| Campo DataService | Fonte | Notas |
|---|---|---|
| `uhe` | Pysis | Usina |
| `re_sgo` | Pysis | Número do relatório de evento |
| `desc` | Pysis | Descrição |
| `recomendacao_id` | Pysis | ID da recomendação |
| `recomendacao` | Pysis | Texto da recomendação |
| `status` | Pysis | Status da recomendação |
| `prazo_base` | Pysis | Prazo base |
| `prazo_vigente` | Pysis | Prazo vigente |
| `responsavel` | Pysis | Responsável |
| `local_instalacao` | Pysis | Local de instalação |
| `data_evento` | Pysis | Data do evento |

---

## 5. OBSERVAÇÕES GERAIS

### 5.1 Conversões e Transformações

1. **Datas e Horas:**
   - No SAP: Armazenadas separadamente (data em formato YYYYMMDD, hora em HHMMSS)
   - No CSV: Colunas separadas com prefixo `dt_` e `hr_`
   - No DataService: Combinadas em formato ISO 8601 com timezone (`YYYY-MM-DDTHH:MM:SS-03:00`)

2. **Flags booleanos:**
   - No SAP/CSV: `'X'` indica verdadeiro, vazio/ausente indica falso
   - No DataService: `true` / `false`

3. **Status:**
   - Múltiplos status históricos são agregados em uma string única, ordenados por hierarquia definida
   - Separados por espaço no campo final

4. **Hierarquia de Local de Instalação:**
   - Formato: `GESTÃO-UHE-SISTEMA-SUBSISTEMA-...`
   - Exemplo: `GHCC-HCCD-UG02-CMAU`
   - Usado para extrair UHE, sistema e subsistema

5. **Agregações:**
   - Custos: Soma de múltiplas linhas do COEP
   - Operações: Lista de operações por ordem
   - Notas por Ordem: Array de notas vinculadas

### 5.2 Chaves de Identificação (TABLE_KEYS)

- **maintenance**: `["nota_sap"]`
- **service-order**: `["ordem"]`
- **asset**: `["local_instalacao", "numero_serie"]`
- **event-report**: `["re_sgo", "recomendacao_id"]`

### 5.3 Tabelas Auxiliares

O arquivo `aux_tables.xlsx` contém:
- **Causa**: Mapeamento de código de causa → texto da causa
- **CenTrab**: Mapeamento de centro de trabalho → descrição
- **TipoNota**: Mapeamento de código de tipo → descrição
- **StatusUsr**: Mapeamento de status de usuário → descrição

### 5.4 Origens de Dados

1. **SAP EP1** (legado): Arquivos sem sufixo `_hana`
2. **SAP HANA** (atual): Arquivos com sufixo `_hana`
3. Ambas as fontes são processadas e consolidadas, mantendo-se a versão mais recente de cada registro

---

## 6. FLUXO DE DADOS

```
SAP (QMEL, AFIH, AUFK, etc.)
    ↓ Extração via Databricks
CSV Files (notas.csv, ordens.csv, etc.)
    ↓ Processamento ETL (Python)
Formatação e Transformação
    ↓ Envio via API
DataService (maintenance, service-order, asset, event-report)
```

### Referências de Código

- Formatação de Notas: [format_extract_notas.py](../src/utils/databricks/format_extract_notas.py)
- Formatação de Ordens: [format_extract_ordens.py](../src/utils/databricks/format_extract_ordens.py)
- Propriedades de Formatação: [formatting_properties.py](../src/configs/formatting_properties.py)
- Propriedades do DataService: [dataservice_properties.py](../src/configs/dataservice_properties.py)
- Comandos DBFS: [dbfs_commands.py](../src/configs/dbfs_commands.py)

---

**Última atualização:** 2025-10-13


Tabelas relacionadas a ordens:

## Tabelas principais de ordens

1. **`aufk`** — Cabeçalho de ordens
   - Contém `AUFNR` (número da ordem de manutenção)
   - Campos: tipo de ordem, descrição, datas, status, etc.

2. **`afih`** — Ordens de manutenção
   - Contém `AUFNR`, `QMNUM` (notificação vinculada)
   - Campos: equipamento, local de instalação, prioridade, etc.

3. **`afko`** — Cabeçalho de ordem (planejamento)
   - Contém `AUFNR`
   - Campos: datas programadas/reais, quantidades, etc.

4. **`afvc`** — Operações de ordens
   - Contém `AUFPL` (plano de ordem)
   - Campos: operações, centros de trabalho, materiais, etc.

5. **`afru`** — Confirmações de ordens
   - Contém `AUFNR`
   - Campos: confirmações de execução, datas reais, quantidades confirmadas, etc.

6. **`afpo`** — Componentes de ordens
   - Contém `AUFNR`
   - Campos: materiais, componentes, quantidades, etc.

## Tabelas relacionadas (custos, status, etc.)

7. **`coep`** — Documentos de custos
   - Contém `AUFNR`, `PAUFNR`
   - Campos: custos planejados/reais, valores, etc.

8. **`jest`** — Status de objetos
   - Relacionada via `OBJNR` (objeto de ordem)
   - Campos: status do sistema e do usuário

9. **`qmel`** — Notificações de manutenção
   - Contém `AUFNR` (ordem associada)
   - Campos: notificações vinculadas às ordens

10. **`iloa`** — Locais de instalação
    - Contém `AUFNR`, `AUFNRI`
    - Campos: localização funcional dos equipamentos das ordens

11. **`resb`** — Reservas de materiais
    - Contém `AUFNR`
    - Campos: reservas de materiais para ordens

12. **`mseg`** — Movimentações de materiais
    - Contém `AUFNR`
    - Campos: movimentações de materiais relacionadas às ordens

## Tabelas secundárias (vendas, compras, etc.)

13. **`bsad`**, **`bsak`**, **`bsas`**, **`bsid`**, **`bsik`** — Documentos contábeis (contas a receber/pagar)
14. **`ekkn`** — Detalhes de compras
15. **`vbak`**, **`vbap`**, **`vbep`** — Vendas (pedidos)
16. **`vbrp`** — Faturas
17. **`glpca`** — Contabilidade geral
18. **`onror`** — Outras referências de ordens
19. **`ppdit`** — Planejamento de produção

**Principais para ETL de ordens:** `aufk`, `afih`, `afko`, `afvc`, `afru`, `coep`, `jest`.