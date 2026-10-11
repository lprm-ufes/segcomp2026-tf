# Trabalho Final — Segurança em Computação
## Prof. Rodolfo Villaça
## Relatório de execução, evidências e achados em dois sistemas propositalmente vulneráveis

---

## 1. Objetivo

Você vai atuar como uma equipe de testes de segurança em um ambiente de laboratório controlado. O trabalho consiste em **executar testes, registrar evidências de cada tentativa e documentar os achados confirmados** em dois sistemas, entregando um **relatório técnico em PDF** e um **vídeo curto** que demonstre o que foi encontrado.

O que se avalia é a qualidade do relatório: um registro honesto do que foi executado (inclusive o que não funcionou), achados reais e comprovados, bem classificados e reproduzíveis. Um achado afirmado sem evidência vale menos do que nenhum achado.

**Sobre o uso de IA.** Você pode e **é recomendado** usar um agente de IA para apoiar os testes. O framework sugerido é o CAI (Cybersecurity AI), com modelo local (Ollama) ou, se o hardware não permitir, um modelo hospedado em nuvem (ver seção 4). O uso é opcional, mas é uma oportunidade de aprendizado profissional: agentes aceleram o reconhecimento e a automação, e o trabalho do analista passa a incluir **validar** o que a IA afirma. Qualquer que seja a ferramenta, o relatório deve declarar o que foi feito por agente e o que foi feito manualmente. O uso de outros agentes é permitido, desde que explicitamente declarado e documentado. O que você não pode fazer é omitir o uso de IA no trabalho.

## 2. Formato

- Grupos de **2 ou 3 pessoas**.
- Entregas: **relatório em PDF (máximo 15 páginas)**, **vídeo (máximo 5 minutos)** e um **arquivo .zip de anexos** (logs e prompts).

## 3. Os dois sistemas e o que se espera de cada um

Ambos rodam em **contêineres na sua própria máquina**. Um kit de laboratório e um tutorial de preparação está disponível no próprio git da disciplina.

**Importante**: o tutorial foi testado localmente em minha própria máquina e funcionou, mas erros locais podem ocorrer e caberá a você corrigi-los e documentar, caso aconteça. Se puder, documente a correção e disponibilize para a turma!

### 3.1. OWASP Juice Shop — mínimo de 2 vulnerabilidades

Aplicação web de loja propositalmente vulnerável, com cobertura ampla do OWASP Top 10.

- Reporte **pelo menos 2 vulnerabilidades**, de **categorias OWASP distintas**.
- No máximo **uma** delas pode ser um desafio de 1 estrela.
- O Juice Shop tem muitos guias públicos. Isso **não é proibido**, ao contrário, é **recomendado**, mas o relatório precisa mostrar que você reproduziu e entendeu o achado (passos, evidência e impacto), e não que copiou uma solução.

### 3.2. Aplicações do professor (SecNote e NetCheck) — mínimo de 2 achados

Duas aplicações web construídas para esta turma, **sem projeto público e sem guia na internet**. O kit entregue ao seu grupo é **individualizado** (um identificador de grupo gera credenciais e dados próprios), então os achados do seu grupo não coincidem com os dos colegas.

- Reporte **pelo menos 2 achados** nessas aplicações.
- Aqui não há como pesquisar a resposta: o raciocínio é seu (ou do agente, mas validado por você). O uso de ferramentas de IA é liberado e também recomendado, desde que devidamente explicitado.

## 4. Ferramentas e IA

Use as ferramentas que preferir (por exemplo nmap, curl, um proxy HTTP como Burp ou ZAP, entre outras) e, se quiser, o agente de IA.

**Recomendação de agente:** CAI, com o tutorial de configuração fornecido. Pontos práticos já testados pelo professor:
- Selecione o persona adequado (`/agent select web_pentester_agent` para alvos web).
- Modelos pequenos locais (7B-8B) funcionam, mas falham com frequência e variam entre execuções. Repita e registre.
- Modelos maiores em CPU, sem GPU dedicada, costumam estourar o tempo limite.
- Se usar um modelo hospedado (ex.: Groq), declare no relatório que deixou de rodar 100% local, e reflita sobre as implicações de privacidade deste tipo de uso. Lembre-se, você estará usando uma LLM pública para achar vulnerabilidades em um sistema interno seu.
- **Desligue a telemetria** (`CAI_TELEMETRY=false` no `.env`) e declare isso.
- **Nunca** coloque chaves de API no relatório, no vídeo, no repositório ou em prints. Confira o `.env` antes de gravar a tela.

**Regra de ouro da validação:** o que um agente (ou uma ferramenta automática) afirma é uma **hipótese**. Todo achado do relatório precisa ser **confirmado por você** com evidência (requisição e resposta, saída de comando, captura de tela).

## 5. Regras de isolamento, ética e segurança (obrigatórias)

1. **Autorização e escopo.** Você só pode testar os sistemas deste trabalho, no ambiente do seu grupo. Testar qualquer outro sistema, inclusive da universidade, **sem autorização expressa** pode configurar crime (Art. 154-A do Código Penal, incluído pela Lei 12.737/2012). 
2. Todos os alvos rodam **apenas em contêineres locais**, com portas publicadas somente em `127.0.0.1`. Nada de expor na rede da universidade, do grupo ou na internet.
3. O agente de IA executa comandos **sem sandbox na sua máquina**. Rode-o em uma máquina virtual ou usuário dedicado, sem dados pessoais acessíveis, e sempre com o alvo explicitamente indicado.
4. Mantenha a possibilidade de **recriar** os contêineres para voltar ao estado inicial.
5. Se um alvo permitir acesso a um shell, ele é o shell **do contêiner**. Não tente sair dele.

## 6. Entregáveis

### 6.1. Relatório (PDF, no máximo 15 páginas)

O limite de 15 páginas vale para o PDF inteiro. **12 páginas** jé é suficiente, mas acima de 16 páginas haverá desconto de pontuação. Um template está fornecido no git da disciplina. A seguir uma **sugestão** de divisão de seções e tamanho por seção. Outras divisões serão aceitas!

| Seção | Conteúdo | Páginas |
|---|---|---|
| 1. Identificação e escopo | Grupo, data, sistemas e versões testados, ambiente de teste, ferramentas, modelo de IA (se houver), divisão do trabalho. | 1 |
| 2. Execução dos testes (log de evidências) | Tabela cronológica com **toda** tentativa relevante, deu certo ou não: data/hora, sistema, ação ou comando testado, como (agente, ferramenta ou manual) e resultado observado, com referência à evidência correspondente no anexo. É o registro do que de fato foi feito, não só do que virou achado. | 1 a 2 |
| 3. Quadro-resumo de achados | Uma linha por achado: ID, sistema, título, CWE/categoria OWASP, severidade (Crítica/Alta/Média/Baixa), como foi encontrado (agente, ferramenta ou manual) e se foi validado manualmente. | 1 |
| 4. Achados detalhados | Um bloco por achado, no formato da seção 6.2. | 5 a 7 |
| 5. Uso do agente e das ferramentas | O que funcionou, o que falhou, alucinações e falsos negativos observados, tempo e custo. Para quem não usou agente: por quê, e que limites sentiu. | 1 a 2 |
| 6. Conclusão | Um parágrafo curto: o que foi encontrado, principais limitações do processo de teste. | 1 |

### 6.2. Formato de cada achado

1. **ID e título** (ex.: `JS-01 — título curto`).
2. **Sistema e componente** afetado.
3. **Classificação**: categoria OWASP e/ou CWE, e **severidade** (Crítica, Alta, Média ou Baixa/Informativa), com uma justificativa curta de 1 a 2 frases (impacto e facilidade de exploração). O vetor CVSS 3.1 completo é **opcional**: quem quiser calculá-lo (calculadora do FIRST) ganha pontos de bônus (seção 7), mas não é exigido.
4. **Descrição**: o que é a falha e por que existe.
5. **Passos de reprodução**: o suficiente para outra pessoa repetir.
6. **Evidência**: requisição/resposta, trecho de log ou captura de tela, **com data e hora** (pode referenciar a linha correspondente do log de execução da seção 6.1).
7. **Impacto**: o que um atacante conseguiria fazer e com que condições.
8. **Recomendação de correção** (opcional): uma frase sugerindo a mitigação, se quiser. Não é obrigatória — o foco do achado é a execução e a evidência, não um plano de remediação. Uma recomendação específica e bem justificada soma pontos de bônus (seção 7).
9. **Como foi encontrado**: agente, ferramenta ou manual.

### 6.3. Anexos opcionais (.zip, fora do limite de páginas)

- Logs brutos das sessões do agente (se usado) e das ferramentas.
- Prompts usados na íntegra (se usado), inclusive os que não funcionaram.

### 6.4. Vídeo (máximo 5 minutos)

Gravação de tela com narração. Sugestão de roteiro:

| Tempo | Conteúdo |
|---|---|
| 0:00 a 0:30 | Grupo, escopo e ferramentas. |
| 0:30 a 3:00 | Demonstração ao vivo de **pelo menos um achado de cada um dos dois sistemas** (pode acelerar trechos longos, mas indique). |
| 3:00 a 4:30 | O que o agente e/ou as ferramentas acertaram e erraram. |
| 4:30 a 5:00 | Principais lições e recomendação mais importante. |

- O vídeo passa de 5:00? A parte excedente **não será avaliada**.

## 7. Avaliação (10 pontos)

| Critério | Pontos |
|---|---|
| **Achados validados e qualidade técnica**: Juice Shop (2,0), aplicações do professor (2,0) | 4,0 |
| **Qualidade do relatório**: log de execução claro e completo, classificação, severidade justificada, evidência e reprodutibilidade | 3,0 |
| **Método e análise**: papel do agente e das ferramentas, falsos positivos e falsos negativos, honestidade sobre limites | 2,0 |
| **Vídeo**: demonstração real e clareza | 1,0 |
| **Total** | **10,0** |

- **Penalidades:** achado apresentado como confirmado sem evidência que o sustente; conduta inadequada - fora das regras de segurança definidas no escopo deste trabalho.                                                                                             .
- **Bônus (+1,0):** achados além do mínimo, encadeamento de falhas bem demonstrado, vetor e nota CVSS 3.1 calculados corretamente para os achados, ou recomendação de correção específica e bem justificada.

## 8. Cronograma sugerido (4 semanas)

| Semana | Foco |
|---|---|
| 1 | Preparar o laboratório e validar a instalação com um teste simples. Decidir ferramentas (e agente). Reconhecimento inicial do Juice Shop. |
| 2 | Juice Shop. **Checkpoint com o professor**: quantos achados validados e quais dificuldades. |
| 3 | Aplicações do professor. Validação manual de todos os achados. Início do relatório. |
| 4 | Fechar relatório, gravar o vídeo, revisar com o checklist abaixo e entregar. |

## 9. Entrega

Prazo: **04/12 (sexta-feira)**. Onde: **Google Classroom** na sala de aula da disciplina. Um envio por grupo, com os nomes de todos os integrantes na capa. O vídeo poderá estar público no Youtube (ou similar), ou link para download no Google Drive (ou similar). Também é possível submeter apenas um link para um Github (ou similar) com toda a documentação disponível.
