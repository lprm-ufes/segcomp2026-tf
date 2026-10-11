# Relatório de Execução, Evidências e Achados

*Segurança em Computação - 2026/2*

*Template de uso opcional quanto às seções e ao formato dos achados. Altere todos os textos entre colchetes e em itálico. Limite total: 15 páginas quando convertido para PDF.*

---

**Grupo:** [nome ou número do grupo]
**Integrantes:** [nome 1 — nome 2 — nome 3]
**Identificador do grupo (GROUP_ID):** [usar o numero de matricula de um dos componentes]

---

## 1. Identificação e escopo

*1 página.*

- **Sistemas e versões testados:** [lista, com versões e imagens usadas].
- **Ambiente:** [sistema operacional, Docker, máquina virtual ou usuário dedicado, rede isolada].
- **Ferramentas:** [nmap, curl, proxy HTTP, outras].
- **Agente de IA:** [usou ou não; framework, modelo, local ou hospedado; se hospedado, declare que deixou de ser 100% local; telemetria desligada: sim/não].
- **Divisão do trabalho:** [quem fez o quê].

## 2. Execução dos testes (log de evidências)

*1 a 2 páginas. Uma linha por tentativa relevante, deu certo ou não — é o registro do que de fato foi feito, não só do que virou achado.*

| Data/hora | Sistema | Ação ou comando testado | Como | Resultado observado | Evidência (anexo) |
|---|---|---|---|---|---|
| | | | | | |
| | | | | | |
| | | | | | |

*"Como": Agente, Ferramenta ou Manual. "Evidência": referência ao arquivo correspondente no .zip de anexos (ex.: `logs/secnote_01.txt`). Achados da seção 4 podem referenciar a linha correspondente deste log.*

## 3. Quadro-resumo de achados

*1 página. Uma linha por achado. "Como": Agente, Ferramenta ou Manual. "Validado": sim somente se você mesmo confirmou com evidência.*

| ID | Sistema | Título | CWE / OWASP | Severidade | Como | Validado |
|---|---|---|---|---|---|---|
| JS-01 | Juice Shop | | | | | |
| JS-02 | Juice Shop | | | | | |
| APP-01 | App | | | | | |
| APP-02 | App | | | | | |

**Escala de severidade:**

| Baixa / Informativa | Média | Alta | Crítica |
|---|---|---|---|
| Impacto limitado ou achado informativo, difícil de explorar | Impacto moderado, ou exploração com pré-condições | Impacto relevante e exploração razoavelmente simples | Impacto grave (ex.: acesso administrativo, vazamento amplo) e exploração simples |

*Classifique cada achado em um desses quatro níveis, com uma justificativa curta (impacto e facilidade de exploração). Calcular o vetor e a nota CVSS 3.1 completos (calculadora do FIRST, first.org/cvss/calculator/3.1) é **opcional** e vale como bônus.*

## 4. Achados detalhados

*Um bloco por achado, no formato abaixo. Entre 5 e 7 páginas no total. O bloco a seguir é um EXEMPLO FICTÍCIO apenas para mostrar o nível de detalhe esperado: apague-o.*

### EXEMPLO-00 — Ausência de cabeçalho de segurança (fictício, apague)

| | |
|---|---|
| **Sistema / componente** | Aplicação web de exemplo, resposta da página inicial. |
| **Classificação** | CWE-1021 (restrição imprópria de quadros de interface) / OWASP A05 (Configuração de segurança incorreta). |
| **Severidade** | Média. Justificativa: impacto limitado à integridade, requer que outro usuário clique em um link malicioso. *(Vetor CVSS opcional, para bônus: `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:L/I:N/A:N`, 4,3 — valores ilustrativos.)* |
| **Descrição** | A resposta HTTP não inclui cabeçalho que impeça a exibição da página dentro de quadros de outros sites. |
| **Passos de reprodução** | 1. Enviar uma requisição GET à página inicial. 2. Inspecionar os cabeçalhos da resposta. 3. Observar a ausência do cabeçalho. |
| **Evidência** | [cabeçalhos da resposta ou captura de tela] |
| **Impacto** | Um site malicioso poderia embutir a página e induzir cliques indevidos de um usuário. |
| **Recomendação (opcional)** | Definir a política de quadros no servidor (por exemplo, restringir ao próprio domínio) e validar em ambiente de teste. |
| **Como foi encontrado** | Ferramenta (inspeção de cabeçalhos). Validado manualmente: sim. |

**Seu achado (copie este bloco para cada achado):**

### [ID] — [título curto do achado]

| | |
|---|---|
| **Sistema / componente** | |
| **Classificação** | [CWE e/ou categoria OWASP] |
| **Severidade** | [Crítica / Alta / Média / Baixa]. Justificativa: [impacto e facilidade de exploração em 1-2 frases]. *(Vetor CVSS opcional, para bônus: CVSS:3.1/...)* |
| **Descrição** | [o que é a falha e por que ela existe] |
| **Passos de reprodução** | [numerados, o suficiente para outra pessoa repetir] |
| **Evidência** | [requisição/resposta, saída de comando, captura de tela, com data e hora] |
| **Impacto** | [o que um atacante conseguiria e sob quais condições] |
| **Recomendação (opcional)** | [sugestão breve de mitigação, se quiser — não é obrigatório] |
| **Como foi encontrado** | [Agente / Ferramenta / Manual]. Validado manualmente: [sim/não] |

## 5. Uso do agente e das ferramentas

*1 a 2 páginas. Seja honesto: um relato franco sobre o que falhou vale mais que um relato otimista.*

- **O que funcionou:** [ ]
- **O que falhou:** [erros de execução, travamentos, tempo]
- **Alucinações (afirmou algo que não existe):** [exemplos e como você descobriu]
- **Falsos negativos (deixou de ver algo que existe):** [exemplos]
- **Tempo e custo:** [tempo total, nº de tentativas, modelo, custo se houve]
- **Sem agente?** [por que, e que limites sentiu comparando com o que se poderia automatizar]

## 6. Conclusão

*Um parágrafo curto ou 0,5 página. O que foi encontrado, no geral, e as principais limitações do processo de teste (tempo, ferramentas, agente).*

[ ]

