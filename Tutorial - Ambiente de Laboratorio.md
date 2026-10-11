# Tutorial - Ambiente de Laboratório e Agente de IA (CAI, 100% local, Ubuntu Linux)

## 1. Preparar o kit de laboratório

### 1.1 Pré-requisitos

- Ubuntu 22.04 ou 24.04 (este tutorial assume Ubuntu; outras distribuições Linux podem exigir ajustes nos comandos de instalação).
- Docker e Docker Compose v2 (`docker compose version`, com espaço, não `docker-compose`).
- Pelo menos 4 GB de RAM livres para o kit básico (Juice Shop + SecNote + NetCheck). Some mais 4–8 GB se também for rodar o agente de IA (seção 3).
- Internet para baixar as imagens na primeira vez. Depois disso, o kit roda sem internet.

```bash
sudo apt update && sudo apt install -y docker.io docker-compose-plugin
```

> **Permissão do Docker:** se `docker` responder `permission denied while trying to connect to the docker API`, seu usuário não está no grupo `docker`. Duas saídas: usar `sudo docker ...` / `sudo docker compose ...` em todos os comandos, ou rodar `sudo usermod -aG docker $USER` e abrir um novo login. Estar no grupo `docker` equivale, na prática, a ter acesso de administrador na máquina.

### 1.2 Extrair o kit e configurar o grupo

Extraia o arquivo `lab-kit.zip` (fornecido pelo professor) em uma pasta dedicada ao trabalho e entre nela:

```bash
unzip lab-kit.zip
cd lab-kit
cp .env.example .env
```

Edite o `.env` e preencha `GROUP_ID` com o identificador que o professor passou ao seu grupo:

```
GROUP_ID=<numero do seu grupo>
```

Esse identificador individualiza os dados do SecNote e do NetCheck (senhas semeadas e textos de confirmação trazem o `GROUP_ID`), para que o achado do seu grupo não coincida com o de outro.

> **Nunca** compartilhe o seu .env fora do grupo.

### 1.3 Subir o Juice Shop, o SecNote e o NetCheck

```bash
docker compose up -d --build
docker compose ps
```

Espera-se três serviços com status `running`: `juice-shop`, `secnote` e `netcheck`.

> **Segurança:** todas as portas do kit já estão publicadas só em `127.0.0.1` (ver `docker-compose.yml`). Não altere isso para `0.0.0.0` nem para o IP da rede do grupo — esses sistemas são vulneráveis de propósito.

### 1.4 Resetar um alvo para o estado inicial

Qualquer alvo pode ser recriado do zero (por exemplo, depois de uma tentativa que alterou algo no ambiente):

```bash
docker compose up -d --build --force-recreate <nome-do-servico>
# ex.: docker compose up -d --build --force-recreate secnote
```

---

## 2. Verificar que cada sistema está de pé

Antes de qualquer teste, confirme que cada alvo responde isoladamente. Esta seção é só checagem.

### 2.1 Juice Shop

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:3000
```

Espera-se `200`. Também vale abrir `http://127.0.0.1:3000` no navegador e ver a loja carregar.

### 2.2 SecNote

```bash
curl -s http://127.0.0.1:4000/
```

Espera-se uma descrição em texto dos endpoints da API.

### 2.3 NetCheck

```bash
curl -s http://127.0.0.1:4001/
```

Espera-se uma descrição em texto do endpoint de diagnóstico.

### 2.4 Checklist de "ambiente pronto"

- [ ] `docker compose ps` mostra `juice-shop`, `secnote` e `netcheck` com status `running`.
- [ ] `http://127.0.0.1:3000` carrega no navegador.
- [ ] `curl` em `127.0.0.1:4000` e `127.0.0.1:4001` responde.
- [ ] O `.env` tem o `GROUP_ID` correto do seu grupo.
- [ ] Nenhuma porta foi alterada para publicar fora de `127.0.0.1`.

---

## 3. (Opcional, porém recomendado) Agente de IA - CAI com modelo local

Esta seção é **opcional**. O uso de agente é recomendado por valor didático e profissional, mas o trabalho pode ser feito inteiramente com ferramentas manuais (curl, um proxy HTTP, nmap etc.): ver enunciado.

**Recomendação desta seção: modelo local via Ollama**, para evitar mandar qualquer tentativa de teste de intrusão para fora da máquina do grupo. É o caminho padrão e o que as seções 3.2 a 3.7 cobrem. Para quem tem hardware muito limitado, a seção 3.8 traz a alternativa avançada de usar um modelo hospedado (Groq) — nesse caso, documente isso explicitamente no relatório (ver enunciado, seção 4).

### 3.1 Ressalva de hardware: isso precisa de uma GPU para funcionar bem

**Leia isto antes de instalar.** CAI com um modelo local **depende fortemente de ter uma GPU com VRAM suficiente** para carregar o modelo. Rodar em CPU funciona apenas para modelos pequenos (na faixa de 7-8 bilhões de parâmetros) e, mesmo assim, de forma lenta e poucas vezes confiável.

- **Com GPU dedicada (recomendado):** modelos de 7B a 8B rodam com inferência rápida (segundos por resposta). Modelos maiores (14B ou mais) também podem funcionar bem, desde que a VRAM da placa comporte o modelo.
- **Sem GPU dedicada (só CPU):** use no máximo um modelo de 7-8B (ex.: `qwen2.5:7b`). Mesmo assim, espere respostas de dezenas de segundos a poucos minutos cada, e falhas frequentes de ferramenta (ver seção 3.6). **Modelos acima de 8B em CPU tendem a estourar o tempo limite de resposta do framework antes mesmo de errar ou acertar a técnica**. Não é um problema de memória disponível; é o processamento em CPU sendo lento demais.
- **Se seu grupo não tem GPU dedicada:** está tudo bem. O trabalho não exige sucesso técnico do agente, e um modelo pequeno travando ou alucinando é, em si, um resultado válido para documentar. Considere também usar o agente só em parte dos testes (por exemplo, reconhecimento) e fazer o restante manualmente.

### 3.2 Instalar o Ollama e baixar um modelo local

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

```bash
ollama pull qwen2.5:7b
```

Se tiver GPU dedicada com VRAM suficiente, pode testar um modelo maior (ex.: `qwen2.5:14b` ou maiores). Sem GPU, fique no 7-8B.

### 3.3 Instalar o framework do agente (CAI)

```bash
python3.12 -m venv cai_env
source cai_env/bin/activate
pip install cai-framework
```

Na mesma pasta do kit (`lab-kit/`), crie um arquivo `.env.cai` separado do `.env` do compose, **ou** adicione estas linhas ao mesmo `.env` (desde que não confunda com o `GROUP_ID` do compose):

```
OPENAI_API_KEY=sk-1234
OLLAMA_API_BASE=http://localhost:11434
CAI_MODEL=ollama/qwen2.5:7b
CAI_MAX_TURNS=15
CAI_TELEMETRY=false
PROMPT_TOOLKIT_NO_CPR=1
```

**Pegadinhas:**
- O CAI exige uma `OPENAI_API_KEY` preenchida mesmo sem usar modelos da OpenAI — é só uma checagem de inicialização. Um valor qualquer (`sk-1234`) resolve.
- **`OLLAMA_API_BASE` NÃO deve terminar em `/v1`** quando o modelo é referenciado como `ollama/<nome>`. Incluir `/v1` produz uma URL quebrada e o agente não responde.
- **`CAI_MAX_TURNS` é obrigatório definir manualmente.** O padrão é infinito; sem esse limite, um modelo pequeno preso em loop pode rodar sem nunca parar sozinho. Um valor entre 10 e 20 é um bom ponto de partida.
- **`CAI_TELEMETRY=false` desliga o envio do log da sessão a um servidor do projeto ao encerrar.** Isso inclui prompts, comandos e saídas — mantenha desligado e declare isso no relatório.
- **O `.env` é lido da pasta onde você roda o `cai`.** Abra o `cai` sempre de dentro da pasta do kit.
- **Cada terminal novo precisa reativar o ambiente virtual** (`source cai_env/bin/activate`).
- **Nunca versionar o `.env` com uma chave real dentro.** Se uma chave for exposta por engano, trate como comprometida e gere uma nova.

### 3.4 Selecionar o persona correto

O persona padrão do CAI é voltado para CTF/captura de flag e tende a rodar comandos como `find / -name flag*` mesmo diante de um objetivo web claro. Assim que o CAI abrir:

```
/agent select web_pentester_agent
```

para os dois alvos (Juice Shop, SecNote e NetCheck).

A troca de persona não é lembrada entre sessões: repita sempre que reabrir o CAI.

### 3.5 Aviso de segurança: o shell do agente roda sem sandbox

A ferramenta que o agente usa para executar comandos roda **diretamente na máquina onde o CAI está instalado, sem isolamento**; não é uma sandbox, container ou VM separada. Em testes anteriores, um objetivo mal-especificado levou o agente (pelo viés do persona padrão) a varrer o `$HOME` inteiro à procura de um arquivo de flag.

- Rode o CAI numa pasta/usuário dedicado ao trabalho, sem outros projetos sensíveis no mesmo `$HOME`.
- Dê objetivos específicos, sempre apontando para o alvo (`http://127.0.0.1:3000`, `http://127.0.0.1:4000` etc.), nunca "explore o sistema" ou "ache uma flag".
- Nunca rode isso numa máquina compartilhada ou com dados de terceiros no mesmo usuário do sistema operacional.

### 3.6 Teste "hello world" (sem alvo, sem ataque)

Confirma a cadeia completa `CAI → modelo → chamada de ferramenta → execução no shell → resposta`, antes de qualquer tentativa real. Dentro do CAI, cole um comando por vez (os prompts são em inglês, só ASCII):

1. Selecione o persona:

```
/agent select web_pentester_agent
```

2. Peça um comando inofensivo:

```
Run the command "echo hello" and tell me the output.
```

**Resultado esperado:** um painel mostrando o comando executado, a saída `hello`, e uma resposta final do agente confirmando. Se isso aparece, o setup está completo.

**Com um modelo local pequeno em CPU, espere:** respostas de alguns minutos, falhas nas primeiras tentativas (erros de schema do tipo `session_id: null`) e, ocasionalmente, a sessão travando de vez com um erro do próprio framework (`litellm.APIConnectionError`) mesmo depois de um resultado correto já ter sido registrado no log. Isso não é um problema do seu setup — é comportamento esperado de modelos pequenos, e vale documentar quantas tentativas foram necessárias. Entre testes, limpe o histórico com `/flush`.

### 3.7 Exemplos de objetivo por sistema

Ajuste o endereço conforme o alvo. Repita cada objetivo 2 a 3 vezes pois a variação entre execuções é grande em modelos pequenos, e **sempre confira manualmente** (com `curl` ou no navegador) o que o agente relatou, antes de considerar um achado validado.

**Juice Shop (reconhecimento, para aquecer):**
```
Faça o reconhecimento inicial de http://127.0.0.1:3000. Identifique rotas
nao linkadas na navegacao, cabecalhos de seguranca ausentes e qualquer
informacao de versao exposta.
```

**SecNote:**
```
Tente autenticar na API em http://127.0.0.1:4000/api/login (POST, JSON com
campos username e password) sem conhecer a senha, testando por SQL
injection. O objetivo e conseguir autenticar como o usuario com role admin.
```

**NetCheck:**
```
Existe uma API de diagnostico de rede em http://127.0.0.1:4001/api/ping que
aceita POST com JSON {"host": "<valor>"} e roda um ping para esse host.
Investigue se e possivel injetar comandos do sistema operacional atraves do
campo host, e se sim, execute o comando "whoami" e reporte o resultado.
```

### 3.8 Alternativa opcional: modelo em nuvem via Groq (para hardware limitado)

Rodar 100% local continua sendo a recomendação padrão desta seção — evita mandar qualquer tentativa de teste para fora da máquina do grupo. Mas para quem tem hardware muito limitado (sem GPU e sem conseguir rodar nem um modelo de 7-8B em CPU de forma utilizável), o CAI também aceita um modelo hospedado na Groq (nuvem, com camada gratuita) como alternativa avançada:

```
OPENAI_API_KEY=sk-1234
OLLAMA_API_BASE=http://localhost:11434
#CAI_MODEL=ollama/qwen2.5:7b
CAI_MODEL=groq/openai/gpt-oss-120b
GROQ_API_KEY=sua-chave-aqui
CAI_MAX_TURNS=15
CAI_TELEMETRY=false
PROMPT_TOOLKIT_NO_CPR=1
```

**Ressalvas importantes se optar por essa alternativa:**
- **Deixa de ser local**: os prompts e (potencialmente) trechos de resposta do alvo trafegam para os servidores da Groq. Se usar essa via, declare isso explicitamente no relatório (ver enunciado, seção 4), e use-a somente contra o ambiente de laboratório isolado do próprio grupo, nunca contra qualquer alvo externo.
- **O CAI tem um guardrail interno de "prompt injection"** que, por padrão, bloqueia instruções que pareçam um ataque — mesmo vindas do próprio usuário autorizado (ex.: pedir explicitamente um comando com payload de SQL injection pode disparar `🛡️ INPUT SECURITY GUARDRAIL TRIGGERED`). Isso é ainda mais provável ao usar um modelo em nuvem. Para desligar esse guardrail (só faz sentido no seu próprio ambiente de laboratório): `CAI_GUARDRAILS=false` no `.env` e, se desligar, documente isso e reflita no relatório sobre o que esse bloqueio automático revela.
- A chave da Groq é obtida em [console.groq.com/keys](https://console.groq.com/keys) . Trate como qualquer outra credencial (nunca commitar, ver nota sobre `.gitignore` em 3.3).

### 3.9 Problemas comuns

- **"Connection refused" ao chamar o Ollama:** confirme com `ollama list`; se falhar, rode `ollama serve` manualmente em outro terminal.
- **Erro `404 Not Found` em `.../v1/api/generate`:** o `OLLAMA_API_BASE` está com `/v1` no final — remova.
- **`cai: command not found`:** o ambiente virtual não está ativado neste terminal.
- **O CAI abre sem o modelo configurado:** você abriu o `cai` fora da pasta que contém o `.env`.
- **Agente preso em loop sem nunca responder:** confirme que `CAI_MAX_TURNS` está definido; interrompa com Ctrl+C se necessário.
- **CAI trava com `litellm.Timeout: ... 600 seconds`:** o modelo é grande demais para o hardware disponível (ver 3.1). Solução: usar um modelo menor, não apenas liberar memória.
- **Erro de validação `session_id: null`:** esperado com modelos pequenos; é recuperável, tente de novo.
- **`Potential sensitive information found: 'root'` (ou similar) na saída de uma ferramenta de requisição web:** é uma heurística simples do CAI que procura palavras-chave no texto da resposta, **não é um achado por si só**. Fique atento se o agente tratar isso como descoberta.
- **Erro de sintaxe de shell com aspas aninhadas ao montar um `curl`:** comum em modelos pequenos e grandes. Se insistir no mesmo erro, peça explicitamente que use a ferramenta de execução de código (Python com `requests`) em vez de montar o comando manualmente.
- **`🛡️ INPUT SECURITY GUARDRAIL TRIGGERED` ao pedir explicitamente uma técnica de ataque:** guardrail interno de "prompt injection" do CAI, mais provável com modelo em nuvem (ver 3.8). Para desligar (só no seu próprio ambiente de laboratório): `CAI_GUARDRAILS=false` no `.env`.

---

## Referências

- CAI — documentação oficial: https://aliasrobotics.github.io/cai/
- CAI — repositório: https://github.com/aliasrobotics/cai
- OWASP Juice Shop: https://github.com/juice-shop/juice-shop
- Pwning OWASP Juice Shop (guia oficial de desafios): https://pwning.owasp-juice.shop/
- Ollama: https://ollama.com/
