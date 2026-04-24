# Matrizes DPE×CT completas do caso ForgePag

> Material complementar ao artigo. Referência cruzada: Tabelas 5 e 6 do artigo principal.

## Operação aplicada à detecção de fraude PIX

### Cadeia de decisão da fraude PIX mapeada aos sete estágios

| Estágio | O que acontece na decisão de fraude PIX |
|---|---|
| 1. Geração | Cliente inicia PIX no app (tap no botão "Enviar") |
| 2. Ingestão | Evento é enviado ao Kafka; produtos de dados em streaming são atualizados |
| 3. Transformação | *Feature store* tempo real computa *features* de sessão e de comportamento; *feature store* batch serve agregações históricas |
| 4. Entrega | Serviço de autorização PIX consulta *feature stores*, APIs síncronas de saldo e limite, e cache de score de risco |
| 5. Inferência | Modelo de score de fraude executa com as *features* consolidadas, produz score no intervalo [0, 1] |
| 6. Decisão | Regra de negócio aplica threshold: score < 0.3 aprova, 0.3-0.7 *step-up*, > 0.7 bloqueia |
| 7. Observação | Resultado é registrado; se houve fraude confirmada posteriormente, label retorna para retreino do modelo e atualização de blocklist |

### Critérios de confiabilidade aplicados

A Tabela 3 do artigo apresenta exemplos concretos derivados deste material. Exemplos de célula:

- **Estágio 2 (Ingestão) × *Freshness*:** latência máxima aceitável de 50ms entre evento PIX e disponibilidade na *feature store* tempo real
- **Estágio 5 (Inferência) × *Distribution*:** distribuição das predições do modelo é monitorada por janela de 1 hora; desvio > 2σ aciona alerta
- **Estágio 7 (Observação) × *Lineage*:** rótulo de fraude confirmada precisa ser rastreável até a transação original via *transaction_id* único

### Critérios de tempestividade aplicados

Calibração por dimensão para fraude PIX:

| Dimensão | Horizonte calibrado |
|---|---|
| Frescor da informação | Milissegundos (features de sessão) a horas (score de destinatário) |
| Frescor do modelo | Retreino adaptativo acionado por drift (Bayram et al., 2024); idade máxima do modelo em produção: 30 dias |
| Janela de oportunidade da decisão | <500ms (antes da autorização ser enviada ao PIX do destinatário) |

### Matriz DPE×CT preenchida para fraude PIX

| | Confiabilidade | Tempestividade |
|---|---|---|
| **Decisão** | A decisão de bloquear fraude está conectada à meta de reduzir perda PIX? SIM (OKR do domínio Risco & Fraude). | A decisão é capaz de atuar antes da liquidação? SIM se <500ms; senão a autorização já foi enviada. |
| **Pergunta** | As 6 perguntas são verificáveis por dados existentes na cadeia? SIM, cada pergunta tem família de *data point* dedicada. | As 6 perguntas são formuladas no momento certo? SIM se no intervalo da autorização; perguntas post-hoc não alimentam a decisão. |
| **Evidência** | Os 30+ *data points* atendem aos 5 pilares em cada um dos 7 estágios? Varia por *feature*; `meds_historico` é frágil em *freshness*, `features_sessao_corrente` é robusto. | Os *data points* estão dentro do horizonte calibrado? Varia por *feature*; `score_bureau` (mensal) é aceito por ser dimensão lenta, `features_sessao_corrente` exige milissegundos. |

## Operação aplicada ao acionamento de retenção

### Cadeia de decisão da retenção mapeada aos sete estágios

| Estágio | O que acontece na decisão de retenção |
|---|---|
| 1. Geração | Eventos comportamentais, transacionais e de atendimento do cliente são produzidos ao longo do dia em múltiplos sistemas-fonte |
| 2. Ingestão | Eventos são capturados em batches diários noturnos pelos *pipelines* dos domínios Engajamento & Retenção e Atendimento |
| 3. Transformação | Agregações históricas, *features* derivadas, métricas de tendência e cálculo dos sete sinais de família são computados no ciclo batch |
| 4. Entrega | Healthy score, churn probability, LTV e value tier são materializados como produtos de dados consumíveis pelos *owners* de domínio |
| 5. Inferência | Modelo de probabilidade de churn executa com o healthy score e metadados do cliente, produzindo probabilidade no intervalo [0, 1] |
| 6. Decisão | Regra de negócio aplica threshold: churn_probability < 0.3 não aciona, 0.3-0.6 aciona intervenção leve, > 0.6 aciona intervenção alta |
| 7. Observação | Taxa de retenção do cliente após 30, 60 e 90 dias da intervenção é observada; sinal retorna para retreino do modelo e ajuste de pesos do healthy score |

### Critérios de confiabilidade aplicados

Exemplos de célula da aplicação dos cinco pilares aos sete estágios para retenção:

- **Estágio 2 (Ingestão) × *Volume*:** volume diário de eventos de sessão recebido deve permanecer na faixa de 150M a 250M; desvios indicam falha na telemetria do app
- **Estágio 3 (Transformação) × *Schema*:** contratos do `engajamento_transacional_agregado` são monitorados por validação de schema em tempo de build do *pipeline*
- **Estágio 4 (Entrega) × *Lineage*:** o healthy score deve rastrear até os sete sinais de família e destes aos *data points* brutos, permitindo auditoria em caso de anomalia
- **Estágio 5 (Inferência) × *Distribution*:** distribuição do churn_probability na base é monitorada semanalmente; desvio > 2σ aciona revisão do modelo
- **Estágio 7 (Observação) × *Freshness*:** sinal de retenção confirmada só é conhecido 30 a 90 dias após a intervenção, condição que introduz latência estrutural no *loop* de aprendizado

### Critérios de tempestividade aplicados

Calibração por dimensão para retenção proativa:

| Dimensão | Horizonte calibrado |
|---|---|
| Frescor da informação | Diário para a maior parte dos sinais de família (agregações D-1); mensal para NPS; milissegundos irrelevantes dada a natureza batch |
| Frescor do modelo | Retreino mensal programado, com disparo antecipado se *drift* for detectado; idade máxima do modelo em produção: 90 dias |
| Janela de oportunidade da decisão | 7 a 30 dias antes do churn previsto; janelas mais curtas comprometem eficácia da intervenção, janelas mais longas geram ruído |

### Matriz DPE×CT preenchida para retenção

| | Confiabilidade | Tempestividade |
|---|---|---|
| **Decisão** | A decisão de acionar retenção está conectada à meta de preservar LTV? SIM (OKR do domínio Engajamento & Retenção). | A decisão é capaz de atuar antes do churn? SIM se acionada com 7 a 30 dias de antecedência; acionamentos tardios não evitam o cancelamento. |
| **Pergunta** | As 7 perguntas são verificáveis por dados existentes nos produtos de dados dos domínios? SIM, cada pergunta tem família dedicada e dados correspondentes. | As 7 perguntas são formuladas no momento certo? SIM no ciclo batch diário; perguntas feitas apenas mensalmente perdem sinais de tendência curta. |
| **Evidência** | Os sinais de família que compõem o healthy score atendem aos 5 pilares? Varia por família; `engajamento_transacional_agregado` é robusto, `reclamacoes_externas` é frágil em *distribution* por depender de fontes externas. | Os sinais estão dentro do horizonte calibrado? Varia por família; NPS (mensal) é aceito por ser dimensão estrutural, `engajamento_transacional_agregado` exige atualização diária. |

## Contraste entre as duas decisões profundas

A aplicação do método DPE às duas decisões evidencia que a matriz DPE×CT acomoda calibrações radicalmente diferentes sem alterar sua estrutura. A tabela abaixo sintetiza o contraste:

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

O contraste sustenta a tese central do método DPE: a generalidade não se dá por ampliação do método, mas por **calibração dos seus critérios** conforme a decisão sob análise.
