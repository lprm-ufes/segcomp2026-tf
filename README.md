# Trabalho Final - Segurança em Computação - 2026/2
## Prof. Rodolfo Villaça - rodolfo.villaca@ufes.br

Este repositório reúne os materiais do trabalho final da disciplina **Segurança em Computação**. Em grupos de 2 ou 3 pessoas, os(as) integrantes executam testes de segurança em um ambiente controlado, registram evidências de cada tentativa e documentam os achados confirmados em dois sistemas: o **OWASP Juice Shop** e um conjunto de **aplicações vulneráveis dedicadas** (SecNote e NetCheck). O uso de um agente de IA (framework CAI, com modelo local) é recomendado como apoio aos testes, mas é opcional.

As entregas são um **relatório técnico em PDF** (execução, evidências e achados) e um **vídeo curto** demonstrando o que foi encontrado. Os detalhes completos, tais como objetivo, regras de segurança e ética, formato do relatório e do vídeo, critérios de avaliação e cronograma, estão no enunciado e nos materias complementares listados abaixo.

## Conteúdo deste repositório

| Arquivo | Conteúdo |
|---|---|
| [Enunciado](Trabalho%20Final%20-%20Relatorio%20de%20Vulnerabilidades%20-%20Enunciado.md) | O enunciado oficial do trabalho: objetivo, formato de entrega, os dois sistemas a testar, ferramentas e uso de IA, regras de isolamento/ética/segurança, formato do relatório e do vídeo, critérios de avaliação e cronograma sugerido. |
| [Tutorial do ambiente](Tutorial%20-%20Ambiente%20de%20Laboratorio.md) | Passo a passo, para Ubuntu Linux, para subir o kit de laboratório com Docker e, opcionalmente, instalar e configurar o agente de IA CAI com modelo local via Ollama (inclui ressalvas de hardware/GPU e uma alternativa opcional de modelo em nuvem). |
| [Template do relatório (Markdown)](Template%20do%20Relatorio%20de%20Vulnerabilidades.md) | Modelo do relatório técnico em Markdown, pronto para preencher: identificação e escopo, log de execução dos testes, quadro-resumo e achados detalhados, uso do agente e conclusão. |
| [lab-kit.zip](lab-kit.zip) | Ambiente de laboratório pronto para uso via `docker compose`: OWASP Juice Shop e as duas aplicações vulneráveis do professor (SecNote e NetCheck), com README próprio de instruções. |

## Como começar

1. Leia o **enunciado** por inteiro antes de tocar em qualquer comando.
2. Siga o **tutorial** para extrair o `lab-kit.zip`, configurar o `GROUP_ID` do seu grupo e subir o ambiente.
3. Use o **template** (Markdown ou Word) para ir registrando o log de execução e os achados à medida que for testando: não deixe para escrever tudo no fim pois a evidência de execução local é IMPRESCINDÍVEL e ESSENCIAL para a avaliação.
4. Grave o vídeo e monte os anexos conforme as seções 6.3 e 6.4 do enunciado.

## Aviso de segurança

Todos os sistemas deste kit são **propositalmente vulneráveis** e devem rodar **apenas em contêineres locais**, com portas publicadas somente em `127.0.0.1`. Teste só os sistemas listados no enunciado, nunca sistemas de terceiros ou da universidade. Nunca inclua suas eventuais chaves de API, senhas ou arquivos `.env` em commits, prints, relatório ou vídeo.
