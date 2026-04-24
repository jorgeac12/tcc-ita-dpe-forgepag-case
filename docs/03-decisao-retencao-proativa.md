# Decisão profunda 2: acionamento proativo de retenção

> Material complementar ao artigo. Referência cruzada: Seção 4.2 do artigo principal.

---

## 1. Camada D (Decisão) — formulação

A decisão automatizada responde à pergunta: este cliente deve ser incluído em campanha proativa de retenção no próximo ciclo operacional? Três resultados são possíveis:

1. **Não acionar:** cliente está saudável, permanece fora da campanha
2. **Acionar com intervenção leve:** cliente recebe comunicação digital personalizada (*push*, e-mail, oferta em app) sem custo humano
3. **Acionar com intervenção alta:** cliente recebe contato humano direto (ligação, *chat* prioritário, gerente dedicado) além da intervenção digital

A decisão é tomada em ciclo batch diário pelo domínio Engajamento & Retenção. A cada madrugada, o serviço de scoring computa o healthy score de todos os clientes ativos e materializa a lista de clientes candidatos a intervenção para o próximo ciclo. Ao contrário da fraude PIX, a decisão não é síncrona no caminho de uma transação: ela é proativa e assíncrona, operando por antecipação.

---

## 2. Camada P (Pergunta) — perguntas que fundamentam a decisão

Sete perguntas formais são respondidas simultaneamente durante o ciclo de scoring:

| ID | Pergunta | Família de *data point* |
|---|---|---|
| P1 | O cliente está engajado transacionalmente em intensidade consistente com seu histórico? | Engajamento transacional |
| P2 | O cliente está utilizando os produtos contratados (cartão, PIX, conta, crédito) em padrão saudável? | Engajamento de produto |
| P3 | A tendência de uso do cliente nos últimos 90 dias é crescente, estável ou decrescente? | Tendência temporal |
| P4 | A qualidade da experiência do cliente é satisfatória, sem fricção elevada? | Qualidade de experiência |
| P5 | A percepção externa do cliente (NPS, indicações) é positiva? | Indicadores externos |
| P6 | O cliente apresenta sinais financeiros saudáveis (movimentação, saldo, uso de crédito adimplente)? | Sinais financeiros |
| P7 | Os sinais de vida do cliente (cadastro, chaves, cartão) indicam conta ativa e pronta para uso? | Sinais de vida |

---

## 3. Camada E (Evidência) — as sete famílias de *data points*

### Família 1 — Engajamento transacional (responde P1)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Logins no app nos últimos 7, 30 e 90 dias | Evento de sessão | Transformação batch | Diário |
| Transferências PIX enviadas nos últimos 30 dias | Agregação histórica | Transformação batch | Diário |
| Pagamentos de boletos nos últimos 30 dias | Agregação histórica | Transformação batch | Diário |
| Compras no cartão nos últimos 30 dias | Agregação histórica | Transformação batch | Diário |
| Frequência semanal de uso (dias da semana com ao menos uma transação) | *Feature* derivada | Transformação batch | Diário |

**Desafios de confiabilidade:** dependência do *pipeline* de eventos de sessão; clientes que usam canais alternativos (Internet Banking, caixas 24h) podem aparecer como inativos mesmo sendo ativos.

### Família 2 — Engajamento de produto (responde P2)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Produtos contratados pelo cliente (Conta, PIX, Cartão, Crédito, Investimentos) | Catálogo de produtos | Tabela batch | Diário |
| Módulos do app abertos nos últimos 30 dias | Eventos de navegação | Transformação batch | Diário |
| Novos produtos ativados nos últimos 90 dias | Evento de ativação | Transformação batch | Diário |
| Tempo médio de sessão no app nas últimas 4 semanas | Agregação de sessão | Transformação batch | Diário |
| Flag de uso de funcionalidades premium (investimentos, limite adicional, cartão múltiplo) | Derivação lógica | Transformação batch | Diário |

**Desafios de confiabilidade:** telemetria de navegação pode ter perda em versões antigas do app; mudanças de *layout* do app alteram rotas de eventos.

### Família 3 — Tendência temporal (responde P3)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Número de transações nos últimos 30 dias vs 30 dias anteriores | *Feature* derivada | Transformação batch | Diário |
| Valor movimentado nos últimos 30 dias vs 30 dias anteriores | *Feature* derivada | Transformação batch | Diário |
| Frequência de login nos últimos 30 dias vs 30 dias anteriores | *Feature* derivada | Transformação batch | Diário |
| Sequência de dias consecutivos sem transação | Contador temporal | Transformação batch | Diário |
| Inclinação da curva de uso ajustada por janela móvel (30/60/90 dias) | *Feature* derivada por regressão local | Transformação batch | Semanal |

**Desafios de confiabilidade:** clientes com padrão sazonal podem ser classificados como em declínio quando estão apenas em ciclo natural; calibração exige histórico suficiente (>180 dias) para ser robusta.

### Família 4 — Qualidade de experiência (responde P4)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Número de tickets de suporte nos últimos 90 dias | Domínio Atendimento | Transformação batch | Diário |
| Tempo médio de resolução dos tickets do cliente | Domínio Atendimento | Transformação batch | Diário |
| Taxa de resolução no primeiro contato (FCR) para este cliente | Domínio Atendimento | Transformação batch | Diário |
| Número de erros técnicos enfrentados pelo cliente (transações recusadas, timeout, falhas de login) | Telemetria de erro | Transformação batch | Diário |
| Latência média experimentada pelo cliente no app (p90 de tempo de resposta) | Telemetria de performance | Transformação batch | Diário |

**Desafios de confiabilidade:** telemetria de erro pode ter lacunas se o app perder conexão; atribuir qualidade a sessões específicas exige *lineage* robusto.

### Família 5 — Indicadores externos (responde P5)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Score NPS mais recente do cliente | Pesquisa NPS | Transformação batch | Mensal |
| Score CSAT dos últimos tickets resolvidos | Pesquisa CSAT pós-atendimento | Transformação batch | Diário |
| Indicações de novos clientes (referral) feitas pelo cliente | Evento de referral | Transformação batch | Diário |
| Avaliações em lojas de aplicativos associadas ao cliente (quando rastreáveis) | Integração externa | Transformação batch | Semanal |
| Reclamações públicas em canais externos (Reclame Aqui, redes sociais) vinculadas ao CPF | Integração externa | Transformação batch | Semanal |

**Desafios de confiabilidade:** pesquisas têm baixa taxa de resposta (10-20%); vinculação a dados externos é probabilística e pode gerar falsos positivos.

### Família 6 — Sinais financeiros (responde P6)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Saldo médio da Conta de Pagamento nos últimos 90 dias | Agregação histórica | Transformação batch | Diário |
| Movimentação média mensal (entradas + saídas) | Agregação histórica | Transformação batch | Diário |
| Utilização percentual do limite de crédito nos últimos 90 dias | Agregação histórica | Transformação batch | Diário |
| Flag de adimplência (em dia, atrasado <30 dias, atrasado >30 dias) | Domínio Crédito | Transformação batch | Diário |
| Presença de recebimentos recorrentes (salário, benefícios, faturamento) | Análise de padrão | Transformação batch | Mensal |

**Desafios de confiabilidade:** classificação de entrada como salário vs outros depende de algoritmo de categorização; clientes com múltiplas fontes bancárias podem parecer menos engajados.

### Família 7 — Sinais de vida (responde P7)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Flag de cadastro atualizado nos últimos 12 meses | Domínio Onboarding | Transformação batch | Diário |
| Chaves PIX ativas e utilizáveis | Domínio PIX | Transformação batch | Diário |
| Cartão físico desbloqueado e ativo | Domínio Cartão | Transformação batch | Diário |
| Último acesso ao Internet Banking ou app | Telemetria de sessão | Transformação batch | Diário |
| Consentimentos LGPD vigentes e termos aceitos | Domínio Onboarding | Transformação batch | Diário |

**Desafios de confiabilidade:** sinais de vida são estáveis mas podem ser desatualizados se integração com outros domínios tiver latência.

---

## 4. Composição do healthy score

O healthy score é computado como função ponderada dos sete sinais de família, cada um normalizado no intervalo [0, 100]. A composição é formalizada como:

```
healthy_score = Σ (wi × scorei),  para i = 1..7
```

Onde:
- `scorei` é o score normalizado [0, 100] da família i
- `wi` é o peso da família i
- `Σ wi = 1` (soma dos pesos é unitária)
- `healthy_score ∈ [0, 100]`

### 4.1 Pesos de referência

Os pesos de referência para o segmento Pessoa Física Geral (*value tier* médio, sem especialização por sub-segmento) são:

| Família | Peso (wi) | Justificativa |
|---|---|---|
| Engajamento transacional | 0,25 | Sinal mais direto de relacionamento ativo |
| Engajamento de produto | 0,15 | Sinaliza profundidade de adoção |
| Tendência temporal | 0,20 | Sinal antecedente mais relevante para churn |
| Qualidade de experiência | 0,10 | Fricção operacional é preditora de abandono |
| Indicadores externos | 0,05 | Taxa de resposta baixa limita peso |
| Sinais financeiros | 0,15 | Saúde financeira condiciona permanência |
| Sinais de vida | 0,10 | Piso de atividade, alto sinal quando ausente |
| **Total** | **1,00** | |

A concentração em Engajamento transacional e Tendência temporal (soma de 0,45) reflete a observação empírica de que os sinais mais preditores de churn em fintechs brasileiras são comportamentais e antecedentes.

### 4.2 Normalização de cada família

Cada score de família é obtido por agregação ponderada dos *data points* da família, com cada *data point* previamente normalizado para [0, 100] segundo seu tipo:

- **Métricas de volume (transações, logins, valores movimentados):** normalização por percentil na distribuição da base ativa. O cliente no percentil 50 recebe score 50; no percentil 90, score 90
- **Métricas binárias ou flags (adimplência, cadastro atualizado, chave PIX ativa):** normalização direta como 0 (ausente) ou 100 (presente)
- **Métricas de tendência (crescimento vs declínio):** normalização em torno do ponto zero, com tendência positiva mapeando para faixa [50, 100] e tendência negativa para [0, 50]
- **Métricas de tempo (dias desde última transação, idade da sessão):** normalização inversa, com tempos curtos próximos de 100 e longos próximos de 0

Detalhes operacionais completos da normalização de cada *data point* são mantidos em catálogo separado no repositório, uma vez que extrapolam o escopo desta avaliação qualitativa.

### 4.3 Exemplo numérico ilustrativo

Para ilustrar a composição, considera-se um cliente fictício com os seguintes scores de família em ciclo batch diário:

| Família | Score normalizado | Peso | Contribuição |
|---|---|---|---|
| Engajamento transacional | 85 | 0,25 | 21,25 |
| Engajamento de produto | 72 | 0,15 | 10,80 |
| Tendência temporal | 60 | 0,20 | 12,00 |
| Qualidade de experiência | 90 | 0,10 | 9,00 |
| Indicadores externos | 78 | 0,05 | 3,90 |
| Sinais financeiros | 88 | 0,15 | 13,20 |
| Sinais de vida | 95 | 0,10 | 9,50 |
| **Healthy score** | | | **79,65** |

Com healthy score 79,65, o cliente seria classificado em faixa de baixa probabilidade de churn no próximo trimestre. A Família 3 (Tendência temporal, score 60) opera como sinal amarelo que merece acompanhamento, mesmo com o score total alto.

### 4.4 Tradução em probabilidade de churn e segmentação

O healthy score final é traduzido em probabilidade de churn no próximo trimestre por modelo calibrado pelo domínio Engajamento & Retenção sobre histórico de rotulagem de churn dos últimos 12 meses. A faixa de valores tipicamente observada:

| Faixa de healthy score | Probabilidade de churn no trimestre | Ação de retenção |
|---|---|---|
| [0, 30] | > 40% | Intervenção alta (contato humano direto) |
| [30, 50] | 20% a 40% | Intervenção alta se *value tier* alto, leve caso contrário |
| [50, 70] | 8% a 20% | Intervenção leve (comunicação digital) |
| [70, 85] | 3% a 8% | Não acionar (monitoramento contínuo) |
| [85, 100] | < 3% | Não acionar (cliente saudável) |

A decisão final de acionamento combina a probabilidade de churn com o *value tier* do cliente (LTV estimado) para priorização de orçamento de campanha.

### 4.5 Ciclo de recalibração

Os pesos `wi` e os *thresholds* de tradução em probabilidade de churn são recalibrados **trimestralmente** pelo domínio Engajamento & Retenção, com as seguintes etapas:

1. Avaliação retrospectiva dos últimos 90 dias: quais clientes com score acima de 70 efetivamente saíram, e quais com score abaixo de 30 permaneceram. Este passo fecha o *loop* de aprendizagem do modelo
2. Análise por sub-segmento: Pessoa Física Geral, Pessoa Física Alto Valor, Pessoa Jurídica Pequena, Pessoa Jurídica Média. Cada sub-segmento pode receber pesos especializados se a análise retrospectiva evidenciar padrões distintivos
3. Calibração de novos pesos por otimização supervisionada sobre a base rotulada
4. Aprovação dos novos pesos pelo comitê de domínio Engajamento & Retenção, com registro de decisão para fins de auditoria e *lineage*
5. Publicação dos novos pesos com versionamento explícito do produto de dado `healthy_score`, permitindo comparabilidade histórica

O ciclo trimestral é justificado pela estabilidade relativa dos padrões comportamentais em horizonte curto, pela disponibilidade de dados rotulados suficientes em 90 dias e pelo custo operacional de recalibração mais frequente. Em situações de mudança abrupta de contexto (lançamento de produto, campanha de aquisição em massa, choque externo regulatório ou macroeconômico), a recalibração pode ser antecipada com aprovação extraordinária do comitê de domínio.

O healthy score alimenta, além da decisão de acionamento de retenção, outras decisões da organização que não são analisadas em profundidade neste documento: oferta de produtos premium e antecipação de limite de crédito. Esta reutilização reforça o status de `healthy_score` como produto de dado com ciclo de vida próprio, sob responsabilidade do domínio Engajamento & Retenção.

---

## 5. Cenário contrafactual para atribuição de valor

A atribuição rigorosa do valor econômico gerado pela decisão de retenção proativa apresenta desafio estruturalmente mais complexo do que a atribuição em outras decisões. Na detecção de fraude, por exemplo, o valor evitado é observável aproximadamente: fraudes bloqueadas deixam registro direto. Na retenção, o cliente que **não** saiu após ser acionado por campanha pode ter permanecido independentemente da intervenção, configurando problema clássico de inferência causal.

### 5.1 Abordagem quase-experimental com grupo de controle

O contrafactual adotado para a retenção é a construção de **grupo de controle quase-experimental** com as seguintes características:

- **Critério de elegibilidade idêntico:** clientes com healthy score abaixo de 50 e no *value tier* elegível para campanha no trimestre
- **Atribuição por amostragem aleatória:** dentre os elegíveis, aproximadamente 10% são alocados ao grupo de controle (sem intervenção) e 90% ao grupo de tratamento (com intervenção apropriada ao *value tier*)
- **Janela de observação uniforme:** 90 dias após a data de potencial acionamento
- **Desfecho observado:** indicador binário de churn confirmado ao final da janela, complementado por valor presente líquido da receita preservada (LTV atribuído)

A proporção de 10% para o grupo de controle resulta de equilíbrio entre rigor estatístico (amostra suficiente para detecção de efeitos) e responsabilidade operacional (não privar a maior parte dos clientes em risco de intervenção potencialmente benéfica).

### 5.2 Métricas de comparação

A comparação entre grupos de tratamento e controle mobiliza as seguintes métricas:

| Métrica | O que mede | Direção esperada |
|---|---|---|
| Taxa de churn no grupo tratamento | % de clientes acionados que saíram em 90 dias | Menor que controle |
| Taxa de churn no grupo controle | % de clientes não acionados que saíram em 90 dias | Baseline |
| Diferença percentual (uplift) | Diferença entre as duas taxas | Positiva |
| Receita preservada líquida | (LTV preservado em tratamento − LTV preservado em controle) − custo de campanha | Positiva |
| Custo por cliente retido | Custo total da campanha / (clientes retidos a mais no tratamento) | Inferior a LTV médio |

### 5.3 Considerações éticas e regulatórias

A utilização de grupo de controle em ambiente operacional real envolve considerações éticas e regulatórias não-triviais:

- Clientes no grupo de controle podem ser interpretados como privados de intervenção potencialmente benéfica, o que exige transparência nos termos de uso e consentimento LGPD
- A alocação aleatória deve respeitar segmentos sensíveis (pessoas em vulnerabilidade financeira confirmada) que podem justificar exclusão do controle por política interna
- O desenho quase-experimental deve ser aprovado por comitê interno de ética de dados, com documentação para auditoria e conformidade regulatória

### 5.4 Limitações da comparação em ambiente simulado

Para os fins deste documento, o desenho contrafactual é declarado como condição teórica de atribuição rigorosa, não como experimento efetivamente executado. A avaliação qualitativa limita-se a demonstrar que o método DPE, aplicado à retenção, **comporta** a definição formal de contrafactual; a execução do experimento e a mensuração precisa do uplift ficam como trabalho futuro. Esta limitação é compartilhada por grande parte da literatura de avaliação de campanhas de retenção em contexto não-experimental, e seu reconhecimento explícito é, em si, contribuição metodológica do método DPE em comparação com abordagens que reportam ganhos sem contrafactual declarado.

---

## 6. Produtos de dados envolvidos

Os *data points* apresentados na Seção 3 são servidos por produtos de dados identificáveis e de *ownership* explícito. A tabela abaixo consolida os principais produtos que alimentam a decisão de retenção proativa.

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

---

## 7. Métricas de valor (OE2 aplicado ao caso)

A decisão de retenção proativa tem efeito predominante em receita preservada, complementando a natureza econômica de outras decisões focadas em custo evitado.

### 7.1 Receita preservada

- **LTV preservado dos clientes retidos:** valor presente líquido dos clientes que não encerraram a conta após intervenção de retenção, descontado do custo da intervenção
- **Receita recorrente mantida:** receita mensal que permanece ativa dos clientes incluídos em campanhas bem-sucedidas
- **Cross-sell induzido:** receita incremental gerada por clientes que ativaram novos produtos como parte da intervenção de retenção

### 7.2 Custo evitado

- **Custo de aquisição de cliente substituto:** R$ 80 a R$ 150 por cliente que precisaria ser adquirido para compensar cada churn, custo evitado quando a retenção é bem-sucedida
- **Custo operacional de reativação:** custo das campanhas de reativação de clientes inativos, mais alto que o custo de retenção proativa

### 7.3 Eficiência operacional

- **Taxa de precisão do acionamento:** proporção de clientes acionados que efetivamente apresentariam churn no horizonte, calibrando o custo/benefício da campanha
- **Taxa de conversão da intervenção:** proporção de clientes acionados que permanecem ativos após 90 dias
- **Custo marginal por cliente retido:** custo total da campanha dividido pelo número de clientes efetivamente retidos

### 7.4 Cenário de referência para atribuibilidade

O cenário contrafactual usado para atribuir valor à decisão de retenção é o **grupo de controle estatisticamente equivalente** não acionado pela campanha. A diferença de taxa de retenção entre o grupo acionado e o grupo de controle, multiplicada pelo LTV médio, é a medida atribuível. Esta abordagem, usada em práticas de marketing quase-experimental, requer desenho cuidadoso de amostragem para evitar viés de seleção.

---

## 8. Operação do método DPE

### 8.1 Cadeia de decisão da retenção mapeada aos sete estágios

| Estágio | O que acontece na decisão de retenção |
|---|---|
| 1. Geração | Eventos comportamentais, transacionais e de atendimento do cliente são produzidos ao longo do dia em múltiplos sistemas-fonte |
| 2. Ingestão | Eventos são capturados em batches diários noturnos pelos *pipelines* dos domínios Engajamento & Retenção e Atendimento |
| 3. Transformação | Agregações históricas, *features* derivadas, métricas de tendência e cálculo dos sete sinais de família são computados no ciclo batch |
| 4. Entrega | Healthy score, churn probability, LTV e value tier são materializados como produtos de dados consumíveis pelos *owners* de domínio |
| 5. Inferência | Modelo de probabilidade de churn executa com o healthy score e metadados do cliente, produzindo probabilidade no intervalo [0, 1] |
| 6. Decisão | Regra de negócio aplica threshold: churn_probability < 0.3 não aciona, 0.3-0.6 aciona intervenção leve, > 0.6 aciona intervenção alta |
| 7. Observação | Taxa de retenção do cliente após 30, 60 e 90 dias da intervenção é observada; sinal retorna para retreino do modelo e ajuste de pesos do healthy score |

### 8.2 Critérios de confiabilidade aplicados

Exemplos de célula da aplicação dos cinco pilares aos sete estágios para retenção:

- **Estágio 2 (Ingestão) × *Volume*:** volume diário de eventos de sessão recebido deve permanecer na faixa de 150M a 250M; desvios indicam falha na telemetria do app
- **Estágio 3 (Transformação) × *Schema*:** contratos do `engajamento_transacional_agregado` são monitorados por validação de schema em tempo de build do *pipeline*
- **Estágio 4 (Entrega) × *Lineage*:** o healthy score deve rastrear até os sete sinais de família e destes aos *data points* brutos, permitindo auditoria em caso de anomalia
- **Estágio 5 (Inferência) × *Distribution*:** distribuição do churn_probability na base é monitorada semanalmente; desvio > 2σ aciona revisão do modelo
- **Estágio 7 (Observação) × *Freshness*:** sinal de retenção confirmada só é conhecido 30 a 90 dias após a intervenção, condição que introduz latência estrutural no *loop* de aprendizado

### 8.3 Critérios de tempestividade aplicados

Calibração por dimensão para retenção proativa:

| Dimensão | Horizonte calibrado |
|---|---|
| Frescor da informação | Diário para a maior parte dos sinais de família (agregações D-1); mensal para NPS; milissegundos irrelevantes dada a natureza batch |
| Frescor do modelo | Retreino mensal programado, com disparo antecipado se *drift* for detectado; idade máxima do modelo em produção: 90 dias |
| Janela de oportunidade da decisão | 7 a 30 dias antes do churn previsto; janelas mais curtas comprometem eficácia da intervenção, janelas mais longas geram ruído |

### 8.4 Matriz DPE×CT preenchida para retenção

| | Confiabilidade | Tempestividade |
|---|---|---|
| **Decisão** | A decisão de acionar retenção está conectada à meta de preservar LTV? SIM (OKR do domínio Engajamento & Retenção). | A decisão é capaz de atuar antes do churn? SIM se acionada com 7 a 30 dias de antecedência; acionamentos tardios não evitam o cancelamento. |
| **Pergunta** | As 7 perguntas são verificáveis por dados existentes nos produtos de dados dos domínios? SIM, cada pergunta tem família dedicada e dados correspondentes. | As 7 perguntas são formuladas no momento certo? SIM no ciclo batch diário; perguntas feitas apenas mensalmente perdem sinais de tendência curta. |
| **Evidência** | Os sinais de família que compõem o healthy score atendem aos 5 pilares? Varia por família; `engajamento_transacional_agregado` é robusto, `reclamacoes_externas` é frágil em *distribution* por depender de fontes externas. | Os sinais estão dentro do horizonte calibrado? Varia por família; NPS (mensal) é aceito por ser dimensão estrutural, `engajamento_transacional_agregado` exige atualização diária. |

---

## 9. Limitações

### 9.1 Definição de churn depende do contexto

Churn pode significar encerramento formal da conta, inatividade prolongada, migração parcial de uso para concorrentes, ou redução significativa de engajamento. A modelagem adotada trata inatividade e cancelamento como proxies, mas há espectro de comportamentos intermediários.

### 9.2 Causalidade vs correlação na retenção

Clientes acionados por campanha e que permanecem ativos podem ter permanecido independentemente da intervenção. A atribuição rigorosa de valor exige grupo de controle, cujo desenho quase-experimental é formalizado na Seção 5, mas não executado neste contexto simulado.

### 9.3 Modelos de LTV são sensíveis a premissas

LTV médio depende de premissas sobre horizonte de retenção, taxa de desconto e estabilidade do modelo de receita, todas simplificadas neste contexto simulado.

### 9.4 Cold start para clientes novos

Clientes com menos de 90 dias de histórico têm healthy score pouco informativo. A decisão de retenção é estruturalmente mais difícil para esse grupo.

### 9.5 Dimensões subjetivas escapam ao modelo

Sinais como NPS e reclamações externas capturam percepção, mas não explicam causas profundas de insatisfação. O healthy score, por ser métrica consolidada, perde granularidade que um diagnóstico qualitativo revelaria.

---

**Fim do documento de Decisão Profunda 2: acionamento proativo de retenção.**
