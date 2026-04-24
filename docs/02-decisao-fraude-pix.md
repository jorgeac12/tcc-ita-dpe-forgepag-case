# Decisão profunda 1: detecção de fraude em transação PIX

> Material complementar ao artigo. Referência cruzada: Seção 4.1 do artigo principal.

---

## Camada D (Decisão) — formulação

A decisão automatizada tem três resultados possíveis:

1. **Aprovar:** transação prossegue sem intervenção
2. **Desafiar com *step-up*:** cliente recebe solicitação de autenticação adicional (biometria, token, pergunta de segurança)
3. **Bloquear:** transação é recusada e cliente é informado

A decisão é tomada pelo serviço de autorização PIX em sequência síncrona no *path* da transação. A decisão é binária (aprovar/não aprovar) com caminho intermediário de *step-up* quando a incerteza está em faixa específica do score de risco.

---

## Camada P (Pergunta) — perguntas que fundamentam a decisão

Seis perguntas formais são respondidas simultaneamente durante a autorização:

| ID | Pergunta | Família de *data point* |
|---|---|---|
| P1 | A geolocalização da transação é consistente com o padrão histórico do cliente? | Geolocalização |
| P2 | O valor é consistente com o comportamento do cliente para este dia do mês e para este destinatário? | Comportamento transacional |
| P3 | O destinatário tem histórico de envolvimento em MEDs ou alertas de fraude? | Rede/grafo |
| P4 | O valor é compatível com o poder aquisitivo observável do cliente? | Poder financeiro |
| P5 | O dispositivo utilizado tem fingerprint reconhecido e histórico saudável? | Dispositivo |
| P6 | Há sinais comportamentais de sessão que indicam engenharia social ou coação? | Comportamento de sessão |

---

## Camada E (Evidência) — *data points* por família

### Família 1 — Geolocalização (responde P1)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Geolocalização do signup (IP, lat/long aproximada) | Evento de cadastro | Geração → persistência | Histórico |
| Geolocalização do último login | Evento de login | Streaming | Minutos |
| Geolocalização da transação atual | Evento transacional | Streaming | Milissegundos |
| Distância entre signup e transação | *Feature* derivada | Transformação | Tempo real |
| Padrão histórico de geolocalização (N últimas transações) | *Feature* agregada | Transformação batch | Diário |

**Desafios de confiabilidade:** VPN, *mobile* vs desktop, imprecisão de geolocalização por IP (pode errar em dezenas de km).

### Família 2 — Comportamento transacional (responde P2)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Valor médio de transação PIX por dia do mês | Agregação 12 meses | Transformação batch | Diário |
| Valor médio de transação para este destinatário | Agregação histórica | Transformação batch | Diário |
| Desvio-padrão do valor por cliente | Agregação histórica | Transformação batch | Diário |
| Número de transações PIX nas últimas 24h | Agregação janela móvel | Streaming | Minutos |
| Flag de primeira transação para este destinatário | Derivação lógica | Transformação | Tempo real |

**Desafios de confiabilidade:** clientes novos (<30 dias) têm histórico incompleto; agregações batch podem atrasar se *pipeline* falhar.

### Família 3 — Rede/grafo (responde P3)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Score de risco do destinatário | Modelo batch | Transformação batch | Horas a diário |
| Quantidade de MEDs envolvendo o destinatário nos últimos 90 dias | Agregação histórica | Transformação batch | Diário |
| Cluster de fraude ao qual o destinatário pertence (se algum) | Análise de grafo | Transformação batch | Semanal |
| Chave PIX do destinatário (tipo: CPF, telefone, chave aleatória) | Metadados da chave | Ingestão | Tempo real |
| Banco do destinatário | Metadados da chave | Ingestão | Tempo real |

**Desafios de confiabilidade:** *lineage* do score MED (de onde vem o sinal? DICT? sistema interno? reportado por outros bancos?); atualização do score depende de eventos reportados por terceiros.

### Família 4 — Poder financeiro (responde P4)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Saldo disponível na Conta de Pagamento | Sistema transacional | Streaming | Tempo real |
| Limite rotativo disponível | Sistema de crédito | API síncrona | Tempo real |
| Salário autodeclarado no onboarding | Dados do cadastro | Histórico | Estático |
| Score de bureau (Serasa, Boa Vista) | Consulta bureau | Transformação batch | Mensal |
| Histórico de movimentação mensal (últimos 6 meses) | Agregação histórica | Transformação batch | Diário |

**Desafios de confiabilidade:** salário autodeclarado sem validação cruzada; *score* de bureau pode estar desatualizado; múltiplas fontes com *lineage* distinto.

### Família 5 — Dispositivo (responde P5)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| *Device fingerprint* (ID agregado de parâmetros do dispositivo) | Evento de sessão | Streaming | Tempo real |
| Versão do sistema operacional | Metadados da sessão | Streaming | Tempo real |
| Histórico deste *fingerprint* (primeira vista, transações anteriores) | Agregação histórica | Transformação | Diário |
| Flag de *rooted*/*jailbroken* | Análise do SO | Streaming | Tempo real |
| Número de contas ForgePag já associadas a este *fingerprint* | Agregação | Transformação batch | Diário |

**Desafios de confiabilidade:** *fingerprint* pode ser falsificado por atacantes sofisticados; identificador pode mudar após atualização do SO.

### Família 6 — Comportamento de sessão (responde P6)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Tempo desde o login até o PIX | Evento de sessão | Streaming | Tempo real |
| Número de tentativas de PIX na sessão | Contador de sessão | Streaming | Tempo real |
| Padrão de digitação (velocidade, erros, pausas) | Telemetria comportamental | Streaming | Tempo real |
| Caminho de navegação até a tela do PIX | Eventos de UI | Streaming | Tempo real |
| Horário da transação (madrugada, fim de semana, horário comercial) | Metadado da transação | Tempo real | Tempo real |
| Valor em formato "redondo" (R$ 500, R$ 1.000, R$ 5.000) | Derivação lógica | Transformação | Tempo real |

**Desafios de confiabilidade:** telemetria comportamental pode ter qualidade variável por dispositivo; falta de histórico para clientes novos.

---

## Cenário contrafactual para atribuição de valor

### Definição do baseline estático

O baseline é composto por regras determinísticas sem aprendizagem, tipicamente utilizadas em operações maduras antes da introdução de modelos de aprendizado de máquina. Exemplos ilustrativos de regras que compõem o baseline:

- Bloqueio quando valor da transação excede R$ 5.000 fora do horário comercial
- Desafio por step-up quando dispositivo é desconhecido e transação ocorre em rota geográfica não usual
- Bloqueio quando destinatário aparece em lista negra construída a partir de ocorrências anteriores no MED
- Limite diário de transações PIX por cliente para novos cadastros (< 30 dias)

O baseline é escolhido por três razões: reflete o estado operacional de muitas instituições antes da adoção de métodos DPE maduros; é reprodutível em ambiente simulado porque suas regras são transparentes; e serve como piso de referência realista, não como adversário artificialmente fraco.

### Métricas de comparação

A comparação entre método DPE aplicado e baseline estático mobiliza as seguintes métricas:

| Métrica | O que mede | Direção esperada |
|---|---|---|
| Taxa de detecção verdadeira (recall) | Fraudes corretamente bloqueadas / total de fraudes | Maior no método DPE |
| Taxa de falsos positivos | Transações legítimas bloqueadas / total de transações legítimas | Menor no método DPE |
| Perda financeira por fraude | R$ perdidos por fraudes não bloqueadas | Menor no método DPE |
| Custo de atrito por step-up excessivo | R$ estimados por clientes que abandonaram transação após step-up | Menor no método DPE |

### Limitações da comparação em ambiente simulado

A comparação em ambiente simulado não substitui experimentação A/B em produção. O baseline estático aqui adotado é aproximação; o baseline real de cada instituição depende de seu histórico específico de regras e exceções. A avaliação qualitativa deste PRD limita-se a demonstrar **que** o método DPE oferece resposta estruturada em todas as seis células da matriz DPE×CT, não **quanto** de ganho adicional ele entrega em instância específica. A quantificação rigorosa é proposta como trabalho futuro na Seção 5.1 do artigo.

---

## Produtos de dados envolvidos

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

---

## Métricas de valor

### Receita incremental

- **Taxa de aprovação de transações legítimas:** proporção de PIX genuínos aprovados sem fricção
- **Receita por cliente preservada:** valor anualizado de clientes que não cancelaram a Conta de Pagamento após experiência positiva de segurança

### Custo evitado

- **Perda por fraude evitada:** R$ de fraudes bloqueadas que, em ausência do método, teriam se concretizado
- **Custo de MED evitado:** custo operacional e regulatório do Mecanismo Especial de Devolução quando bloqueio preventivo é bem-sucedido
- **Multas regulatórias evitadas:** penalidades por descumprimento de diretrizes BACEN sobre prevenção de fraude

### Eficiência operacional

- **Throughput do serviço de autorização:** número de decisões por segundo que o serviço sustenta
- **Taxa de *step-up* adequado:** proporção de desafios que efetivamente bloqueiam fraude versus falsos positivos
- **Tempo médio de análise manual:** horas economizadas em revisão humana por fraudes detectadas automaticamente

### Cenário de referência para atribuibilidade

O cenário contrafactual usado para atribuir valor à decisão automatizada é a **regra baseline** de bloqueio somente por lista estática de destinatários conhecidos e por valor acima de limite fixo. A diferença de perda por fraude entre o método DPE e o cenário baseline é a medida atribuível.

---

## Operação do método DPE

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

---

## Limitações específicas da decisão de fraude PIX

1. **Diversidade de padrões de fraude não exaurida:** o caso simulado considera seis famílias de sinais, mas fraudadores sofisticados exploram padrões emergentes que exigem atualização contínua do catálogo
2. ***Trade-off* entre segurança e experiência:** bloqueios excessivos geram fricção em clientes legítimos; falsos positivos acarretam custo de reputação não modelado quantitativamente aqui
3. **Integração com DICT e BACEN:** a modelagem assume integração plena com o Diretório de Identificadores de Contas Transacionais e com o sistema de MED, que na prática apresenta latências e falhas não tratadas no caso simulado

---

**Documento composto a partir das Seções 3, 5.1, 6.1 e 7.1 do PRD — ForgePag Case**
