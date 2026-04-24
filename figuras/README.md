# Figuras do artigo principal e do caso aplicado ForgePag

Figuras utilizadas no artigo principal e neste repositório como material complementar. Figuras 1 e 2 sustentam a exposição do método DPE (Seção 3 do artigo); Figuras 3, 4 e 5 instanciam o caso ForgePag (Seção 4 do artigo).

## Inventário

| Arquivo | Figura no artigo | Descrição |
|---|---|---|
| `fig01-cadeia-decisao-automatizada.png` | Figura 1 | Cadeia de decisão automatizada em sete estágios (Geração, Ingestão, Transformação, Entrega, Inferência, Decisão, Observação). Estrutura processual genérica que o método DPE utiliza como espinha dorsal para instanciar a matriz DPE×CT. |
| `fig02-matriz-dpe-ct.png` | Figura 2 | Matriz DPE×CT do método DPE. Três camadas processuais (Decisão, Pergunta, Evidência) por dois eixos qualificadores (Confiabilidade e Tempestividade), com as seis células que orientam a calibração dos critérios de cada decisão automatizada. |
| `fig03-timeline-fraude-pix.png` | Figura 3 | Linha do tempo da decisão de detecção de fraude em PIX. Escala em milissegundos, janela total de oportunidade < 500ms. Mostra os estágios da cadeia (ingestão de *features* de sessão, transformação e consulta a *feature stores*, inferência do modelo de score de fraude, aplicação da regra de decisão e *step-up*) com *deadline* de autorização em 500ms. |
| `fig04-timeline-retencao.png` | Figura 4 | Linha do tempo da decisão de acionamento proativo de retenção. Escala em dias (Dia 0 a Dia 90), com coleta e geração de sinais nos primeiros 60 dias, decisão de campanha por volta do Dia 60, janela de intervenção de 7 a 30 dias antes do *churn* previsto e janela de observação pós-decisão até o Dia 90. |
| `fig05-composicao-healthy-score.png` | Figura 5 | Composição do *healthy score* como evidência derivada. Três camadas: Camada 1 (*data points* brutos, mais de 30 itens em 7 grupos), Camada 2 (famílias de sinais agregadas com scores e pesos), Camada 3 (métrica consolidada `healthy_score` = 79,65/100, probabilidade de *churn* baixa). Destaque para o conceito de evidência derivada versus evidência direta. |

## Formato

Todas as figuras estão em formato PNG, resolução adequada para reprodução em artigo acadêmico e visualização em tela.

## Referência cruzada

As figuras são referenciadas nos seguintes documentos deste repositório:

- `docs/01-prd-caso-forgepag.md` → Figuras 1, 2, 3, 4 e 5 (mobilização do método e do caso)
- `docs/02-decisao-fraude-pix.md` → Figura 3
- `docs/03-decisao-retencao-proativa.md` → Figuras 4 e 5
- `docs/06-matriz-dpe-ct-completa.md` → Figuras 2, 3, 4 e 5
