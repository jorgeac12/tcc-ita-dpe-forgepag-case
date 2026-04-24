# Catálogo de dados e features do caso ForgePag

> Material complementar ao artigo. Consolida os produtos de dados das Seções 5.1 e 5.2 do PRD.

## Produtos de dados da decisão de fraude PIX

| Produto de dado | Domínio owner | Tipo | Frescor contratado |
|---|---|---|---|
| `clientes_cadastro` | Onboarding | Tabela batch | Diário |
| `kyc_eventos` | Onboarding | Stream | Tempo real |
| `device_fingerprint` | Onboarding | Stream + tabela histórica | Tempo real / Diário |
| `saldo_diario` | Conta de Pagamento | Tabela batch + cache tempo real | Diário / Tempo real |
| `limite_disponivel` | Conta de Pagamento | API síncrona | Tempo real |
| `movimentacao` | Conta de Pagamento | Tabela batch | Diário |
| `transacao_pix_stream` | PIX | Stream | Tempo real |
| `chave_pix` | PIX | Tabela + API | Tempo real |
| `meds_historico` | PIX | Tabela batch | Diário |
| `score_bureau` | Crédito | API externa + cache | Mensal |
| `score_fraude_destinatario` | Risco & Fraude | Modelo batch + cache | Horas |
| `blocklist_destinatarios` | Risco & Fraude | Tabela + cache | Minutos |
| `features_comportamentais_cliente` | Risco & Fraude | *Feature store* | Diário |
| `features_sessao_corrente` | Risco & Fraude | *Feature store* tempo real | Tempo real |

## Produtos de dados da decisão de retenção proativa

| Produto de dado | Domínio owner | Tipo | Frescor contratado |
|---|---|---|---|
| `sessao_login_historico` | Onboarding | Tabela batch | Diário |
| `engajamento_transacional_agregado` | Engajamento & Retenção | Tabela batch | Diário |
| `engajamento_produto_agregado` | Engajamento & Retenção | Tabela batch | Diário |
| `tendencia_uso_cliente` | Engajamento & Retenção | *Feature store* | Diário |
| `healthy_score` | Engajamento & Retenção | Tabela batch | Diário |
| `churn_probability` | Engajamento & Retenção | Modelo batch | Diário |
| `ltv_cliente` | Engajamento & Retenção | Modelo batch | Semanal |
| `value_tier_cliente` | Engajamento & Retenção | Tabela batch | Diário |
| `campanhas_ativas` | Engajamento & Retenção | Tabela batch | Diário |
| `tickets` | Atendimento | Stream + tabela batch | Tempo real / Diário |
| `nps_scores` | Atendimento | Tabela batch | Mensal |
| `tempo_resolucao_ticket` | Atendimento | Tabela batch | Diário |
| `csat_pos_atendimento` | Atendimento | Tabela batch | Diário |
| `erros_experiencia` | Atendimento | Stream + tabela batch | Tempo real / Diário |
| `latencia_p90_cliente` | Atendimento | Tabela batch | Diário |
| `reclamacoes_externas` | Atendimento | Tabela batch | Semanal |
| `referrals_feitos` | Engajamento & Retenção | Tabela batch | Diário |
| `consentimentos_lgpd` | Onboarding | Tabela batch | Diário |

## Contraste entre as duas decisões profundas

| Dimensão | Fraude PIX | Retenção proativa |
|---|---|---|
| Natureza econômica | Custo evitado | Receita preservada |
| Tempestividade da decisão | Milissegundos | Dias a semanas |
| Ciclo de operação | Síncrono, tempo real | Batch diário |
| Observação de resultado | Horas a dias | 30 a 90 dias |
| Natureza da evidência | *Features* consumidas diretamente pelo modelo | Healthy score consolidado (evidência derivada) |
| Número de *data points* brutos | 30+ | 35+ |
| Retreino do modelo | Adaptativo por *drift* | Programado mensal + adaptativo |
| Risco predominante no eixo C | *Freshness* e *lineage* em tempo real | *Distribution* e *volume* em batch |
| Risco predominante no eixo T | Janela de oportunidade menor que latência da cadeia | *Drift* lento mascarado por janelas longas |
