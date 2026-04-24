# tcc-ita-dpe-forgepag-case

Material complementar ao artigo de Trabalho de Conclusão de Curso (TCC) do programa de Especialização em Ciência de Dados (CEDS) do Instituto Tecnológico de Aeronáutica (ITA), intitulado **"Método para geração de valor mensurável em decisões automatizadas por inteligência artificial: confiabilidade, tempestividade e conexão com métricas de negócio"**. Este repositório hospeda o detalhamento completo do caso aplicado apresentado na Seção 4 do artigo, preservando o limite de páginas exigido pelo padrão da Sociedade Brasileira de Computação (SBC) sem sacrificar a profundidade técnica necessária para arguição em banca.

## O método DPE

O método DPE (Decisão, Pergunta, Evidência) propõe uma sequência prescritiva para conectar decisões automatizadas por inteligência artificial (IA) a métricas de negócio mensuráveis. Partindo da decisão de negócio, o método DPE deriva a pergunta que a decisão precisa responder e, em seguida, a evidência empírica (dados, *features*, modelos e indicadores) necessária para sustentar aquela resposta com confiabilidade e tempestividade adequadas ao impacto esperado na cadeia de valor. A matriz DPE×CT (três camadas processuais por dois eixos qualificadores) constitui o instrumento integrador do método.

## A fintech ForgePag

ForgePag é uma fintech brasileira fictícia, concebida como ambiente de avaliação controlada do método DPE. Seu produto principal é uma Conta de Pagamento com suporte ao Pagamento Instantâneo Brasileiro (PIX), contexto que impõe decisões automatizadas sob restrição simultânea de risco (fraude), latência (janela regulatória de liquidação) e experiência do cliente (retenção). O caso foi desenhado para expor o método DPE a decisões de naturezas e criticidades contrastantes, permitindo evidenciar que a generalidade do método se dá por calibração dos eixos de Confiabilidade e Tempestividade, não por reformulação da estrutura processual.

## Como este repositório se relaciona com o artigo

O artigo SBC apresenta, na Seção 4, a aplicação e os resultados consolidados do caso ForgePag, estruturada em três subseções: Seção 4.1 (detecção de fraude em PIX), Seção 4.2 (acionamento proativo de retenção) e Seção 4.3 (discussão, limitações e material complementar). A URL deste repositório é citada na abertura da Seção 4 do artigo. Este repositório complementa o artigo com os seguintes materiais:

- Documento de Requisitos de Produto (*Product Requirements Document*, PRD) completo do caso simulado.
- Detalhamento individual das duas decisões profundas analisadas: detecção de fraude em transação PIX e acionamento proativo de retenção para clientes com sinal de *churn*.
- Catálogo expandido de dados e *features* utilizados como evidência na aplicação do método DPE.
- Matrizes DPE×CT preenchidas na íntegra, incluindo cadeia de decisão em sete estágios, critérios de confiabilidade e tempestividade aplicados e contraste entre as duas decisões.
- Figuras do artigo principal (linhas do tempo das decisões e composição do *healthy score*).

Cada documento referencia explicitamente a seção, tabela ou figura correspondente no artigo principal, permitindo navegação cruzada entre o artigo e o anexo.

## Navegação pelas pastas

```
tcc-ita-dpe-forgepag-case/
├── docs/
│   ├── 01-prd-caso-forgepag.md        # PRD completo (documento-mestre)
│   ├── 02-decisao-fraude-pix.md        # Decisão profunda 1 → Seção 4.1 do artigo
│   ├── 03-decisao-retencao-proativa.md # Decisão profunda 2 → Seção 4.2 do artigo
│   ├── 05-dados-e-features.md          # Catálogo consolidado de produtos de dados
│   └── 06-matriz-dpe-ct-completa.md    # Matrizes DPE×CT preenchidas + contraste
├── figuras/
│   ├── README.md                       # Inventário e descrição das figuras
│   ├── fig01-cadeia-decisao-automatizada.png # Figura 1 do artigo
│   ├── fig02-matriz-dpe-ct.png         # Figura 2 do artigo
│   ├── fig03-timeline-fraude-pix.png   # Figura 3 do artigo
│   ├── fig04-timeline-retencao.png     # Figura 4 do artigo
│   └── fig05-composicao-healthy-score.png # Figura 5 do artigo
├── CITATION.cff
├── LICENSE
└── README.md
```

A leitura sugerida inicia pelo PRD (`docs/01-prd-caso-forgepag.md`) e avança para as decisões profundas, para o catálogo de dados e, por fim, para as matrizes DPE×CT.

## Como citar

As instruções de citação estão formalizadas em `CITATION.cff`, seguindo o padrão *Citation File Format* 1.2.0. A referência principal em qualquer trabalho derivado deve ser o artigo SBC; este repositório deve ser citado como anexo do artigo.

## Licença

Distribuído sob licença MIT. Consulte o arquivo `LICENSE` para os termos completos.

## Autoria e orientação

- **Autores:** Jorge Kennedy S. Oliveira e Joao Paulo L. S. Polotto, programa CEDS, Instituto Tecnológico de Aeronáutica (ITA).
- **Orientador:** Prof. Dr. Johnny Cardoso Marques, Instituto Tecnológico de Aeronáutica (ITA).
