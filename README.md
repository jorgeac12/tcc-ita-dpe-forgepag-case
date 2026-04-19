# tcc-ita-dpe-forgepag-case

Material complementar ao artigo de Trabalho de Conclusão de Curso (TCC) do programa de Especialização em Ciência de Dados (CEDS) do Instituto Tecnológico de Aeronáutica (ITA), intitulado "Método DPE para geração de valor mensurável em decisões automatizadas por inteligência artificial: confiabilidade, tempestividade e conexão com métricas de negócio". Este repositório hospeda o detalhamento completo do caso aplicado apresentado na Seção 4 do artigo, preservando o limite de páginas exigido pelo padrão da Sociedade Brasileira de Computação (SBC) sem sacrificar a profundidade técnica necessária para arguição em banca.

## O método DPE

O método DPE (Decisão, Pergunta, Evidência) propõe uma sequência prescritiva para conectar decisões automatizadas por inteligência artificial (IA) a métricas de negócio mensuráveis. Partindo da decisão de negócio, o método DPE deriva a pergunta que a decisão precisa responder e, em seguida, a evidência empírica (dados, *features*, modelos e indicadores) necessária para sustentar aquela resposta com confiabilidade e tempestividade adequadas ao impacto esperado na cadeia de valor.

## A fintech ForgePag

ForgePag é uma fintech brasileira fictícia, concebida como ambiente de avaliação controlada do método DPE. Seu produto principal é uma Conta de Pagamento com suporte ao Pagamento Instantâneo Brasileiro (PIX), contexto que impõe decisões automatizadas sob restrição simultânea de risco (fraude), latência (janela regulatória de liquidação) e experiência do cliente (retenção e priorização de atendimento). O caso foi desenhado para expor o método DPE a decisões de naturezas e criticidades distintas, permitindo evidenciar a generalidade da proposta sem recorrer a dados proprietários de instituições reais.

## Como este repositório se relaciona com o artigo

O artigo SBC apresenta, na Seção 4, o escopo, a aplicação e os resultados consolidados do caso ForgePag. Este repositório complementa o artigo com os seguintes materiais:

- Documento de Requisitos de Produto (*Product Requirements Document*, PRD) completo do caso simulado.
- Detalhamento individual das duas decisões profundas analisadas (detecção de fraude no PIX e acionamento proativo de retenção de cliente) e da decisão comparativa (priorização da fila de atendimento).
- Catálogo expandido de dados e *features* utilizados como evidência na aplicação do método DPE.
- Matrizes DPE preenchidas na íntegra, incluindo a composição com os pilares de confiabilidade e tempestividade.
- Expansão das tabelas apresentadas no artigo e descrição textual das figuras, destinada a servir como fonte para construção visual.

Cada documento referencia explicitamente a seção, tabela ou figura correspondente no artigo principal, permitindo navegação cruzada entre o artigo e o anexo.

## Navegação pelas pastas

- `docs/`: detalhamento textual do caso aplicado, organizado em sequência numerada. A leitura sugerida inicia pelo PRD (`01-prd-caso-forgepag.md`) e avança para as decisões profundas, para o catálogo de dados e, por fim, para as matrizes DPE.
- `tabelas/`: expansão em Markdown das tabelas do artigo, mantendo a numeração utilizada no texto principal.
- `figuras/`: descrições textuais das figuras do artigo, utilizadas como fonte para construção visual e para garantir reprodutibilidade da representação.

## Como citar

As instruções de citação estão formalizadas em `CITATION.cff`, seguindo o padrão *Citation File Format* 1.2.0. A referência principal em qualquer trabalho derivado deve ser o artigo SBC; este repositório deve ser citado como anexo do artigo.

## Licença

Distribuído sob licença MIT. Consulte o arquivo `LICENSE` para os termos completos.

## Autoria e orientação

- **Autor:** Jorge Kennedy S. Oliveira, programa CEDS, Instituto Tecnológico de Aeronáutica (ITA).
- **Orientador:** Prof. Dr. Johnny Cardoso Marques, Instituto Tecnológico de Aeronáutica (ITA).
