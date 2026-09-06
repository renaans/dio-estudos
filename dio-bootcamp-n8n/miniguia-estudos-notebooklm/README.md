# 🤖 Miniguia de Estudos: Arquitetura de Agentes de IA e Automação no n8n
> Repositório desenvolvido para o Desafio de Projeto do Bootcamp Santander - Automação com n8n (DIO), utilizando o Google NotebookLM como ferramenta de aprendizagem ativa e pesquisa fundamentada.

---

## 🎯 1. Contexto e Objetivos

### Contexto
* **Tema escolhido:** Automação com n8n e Agentes de IA.
* **Motivação:** Escolhi este tema porque quero entender na prática o funcionamento da automação no n8n integrada com a mecânica real dos agentes de inteligência artificial, indo além dos fluxos básicos e tradicionais.

### Objetivos de Estudo
* Compreender como o nó de AI Agent do n8n raciocina e toma decisões durante o fluxo.
* Entender como os agentes usam ferramentas externas para consultar APIs, bancos de dados e executar ações.
* Identificar como funciona a memória dos agentes para manter o contexto das conversas.
* Mapear os cuidados necessários para evitar erros comuns e execuções infinitas nos fluxos.

---

## 📑 2. Curadoria de Fontes

Para alimentar o caderno no NotebookLM com autoridade técnica, foram selecionadas as seguintes fontes:

* **Link 1:** Explica o funcionamento do nó principal do agente e suas configurações gerais no n8n.  
  [n8n Docs - AI Agent](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent/)
* **Link 2:** Detalha como o agente aciona ferramentas externas e usa parâmetros dinâmicos via `$fromAI()`.  
  [n8n Docs - Tools Agent](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent/tools-agent/)
* **Link 3:** Mostra as diferenças práticas entre memória temporária em buffer e memória persistente em banco de dados.  
  [n8n Docs - How Memory Works](https://docs.n8n.io/build/integrate-ai/understand-ai-components/how-memory-works/)
* **Link 4:** Apresenta a arquitetura básica do LangChain e a divisão entre nós principais e sub-nós no n8n.  
  [n8n Docs - LangChain in n8n](https://docs.n8n.io/build/integrate-ai/langchain-in-n8n/)
* **Link 5:** Traz a base teórica do padrão ReAct (raciocínio antes da ação), lógica usada pelo agente para tomar decisões.  
  [Artigo Científico ReAct - arXiv:2210.03629](https://arxiv.org/abs/2210.03629)

---

## 🛠️ 3. Engenharia de Prompts e "Cicatrizes" (Troubleshooting)

### Etapa 1: Pergunta Inicial (Abordagem Direta)
* **Pergunta:**  
  > *"Para que serve a expressão `$fromAI()` na configuração dos parâmetros de uma ferramenta no n8n?"*
* **Resposta obtida:**  
  A IA retornou uma resposta correta, porém extremamente curta e superficial (duas frases):
  > *"A expressão `$fromAI()` serve para preencher de forma dinâmica os parâmetros de ferramentas conectadas a nós de aplicativos (app node tools) no n8n. Ao usá-la, você permite que a própria Inteligência Artificial determine e especifique os valores que devem ser inseridos nesses campos durante a execução do fluxo de trabalho."*

---

### Etapa 2: Refinamento Técnico (Exigindo Detalhes de Engenharia)
* **Ajuste no Prompt:**  
  > *"Com base estrita nas fontes que carreguei, aprofunde a explicação técnica em tópicos e aponte os campos exatos do n8n envolvidos."*
* **Salto de Qualidade Obtido:**  
  A IA mapeou detalhadamente as camadas da integração:
  1. **Mapeamento de Arquitetura (LangChain & n8n):** Diferenciou *Root Nodes* (`AI Agent`, cadeias e vector stores) e *Sub-nodes* (`Language Models`, `Memory`, `Tools`, `Output Parsers`).
  2. **Configuração de Entrada (Prompt):** Mapeou as opções `Take from previous node automatically` (via `chatInput`) e `Define below` (`Prompt User Message`).
  3. **Chamada de Ferramentas e `$fromAI()`:** Detalhou o uso da interface de tool calling do LangChain e a identificação dinâmica de variáveis em nós de app (Gmail, Slack, Sheets, Postgres).
  4. **Controle de Formato de Saída:** Demonstrou como `Require Specific Output Format` aciona parsers como *Structured Output Parser*.
  5. **Gerenciamento de Memória:** Destacou que memória é exclusiva do AI Agent (não funciona em chains) e listou sub-nós como `Simple Memory`, `Postgres Chat Memory` e o utilitário `Chat Memory Manager`.
  6. **Parâmetros de Execução do Nó:** Listou `System Message`, `Max Iterations` (padrão 10 para barrar loops), `Return Intermediate Steps`, `Streaming` e `Tracing Metadata`.
  7. **Revisão Humana (Human-in-the-loop):** Apontou o fluxo de autorização manual no painel da ferramenta para evitar ações destrutivas.
  8. **Depuração via LangSmith:** Mapeou variáveis de ambiente necessárias em instâncias *self-hosted* (`LANGCHAIN_TRACING_V2`, `LANGCHAIN_API_KEY`, etc.).

---

### Etapa 3: Refinamento Pedagógico (O Papel de Professor / Mentor)
Para absorver o conteúdo técnico com máxima clareza e analogias práticas, estruturei um novo prompt:

* **Prompt Estruturado:**  
  > *"Atue como o meu Professor e Mentor em automação com IA. Eu quero aprender de verdade como o n8n funciona por baixo do capô, mas preciso de uma didática clara: use os termos técnicos exatos da área, mas em seguida me explique em miúdos e com analogias simples o que cada coisa significa na prática.*  
  >  
  > *Com base nas fontes que carreguei sobre as ferramentas e o agente no n8n, me ensine tudo sobre a expressão `$fromAI()`:*  
  > *1. O Conceito Técnico e a Analogia (termo técnico vs. valor estático).*  
  > *2. Como a Mágica Acontece por Baixo dos Panos (geração de esquemas/schemas).*  
  > *3. A Extração Semântica na Prática (casos com datas relativas ou termos indiretos).*  
  > *4. O que acontece quando falta informação? (comportamento de resiliência e Max Iterations).*  
  > *5. Ao final, me dê uma dica de mestre: qual é o erro mais comum que iniciantes cometem ao usar `$fromAI()` e como evitar?"*

* **Principais Aprendizados Consolidados:**
  * **Conceito Técnico:** Delegação Dinâmica de Parâmetros por Esquema (*Schema-based Parameter Delegation*).  
    * *Analogia:* Preencher valor fixo é como comprar uma passagem de ônibus impressa (o destino nunca muda). Usar `$fromAI()` é como contratar um motorista inteligente e dizer: *"Leve-me ao lugar que eu citar durante nossa conversa"*.
  * **Mecânica por Baixo dos Panos:** O n8n varre os campos marcados com `$fromAI()` e monta um **JSON Schema** declarativo enviado ao modelo, dizendo exatamente o nome, tipo e descrição do dado que a IA deve preencher.
  * **Extração Semântica (ReAct):** O modelo interpreta termos relativos (ex: *"avise meu chefe que chego amanhã"*), converte referências temporárias em datas absolutas e busca no histórico da conversa o e-mail correspondente.
  * **Resiliência:** Se faltarem dados essenciais, o ciclo ReAct impede que o fluxo quebre. Em vez de disparar a ferramenta vazia, a IA pergunta de volta ao usuário para completar a instrução.
  * **Dica de Mestre (A Armadilha do Iniciante):** Usar `$fromAI()` em conversas de múltiplos turnos **sem conectar um nó de memória** ao AI Agent faz o agente esquecer dados ditos em mensagens anteriores, travando a automação em perguntas repetitivas.

---

## 📘 4. Miniguia de Estudo (Entrega Consolidada)

### 4.1 Resumo Estruturado da Arquitetura
* **Nó Raiz (AI Agent):** Orquestra o raciocínio, limites (`Max Iterations`) e instruções mestras (`System Message`).
* **Ferramentas (Tools):** Estendem o poder do agente permitindo interações com sistemas externos por meio de esquemas declarativos.
* **Expressão `$fromAI()`:** Habilita a extração semântica autônoma de parâmetros a partir do diálogo com o usuário.
* **Memória (Chat Memory):** Obrigatória para retenção de contexto entre turnos de mensagens; a *Simple Memory* atende testes locais, enquanto bancos relacionais (`Postgres Chat Memory`) asseguram persistência multiusuário.

### 4.2 Glossário Rápido de Termos Técnicos
* **Root Node:** Nó central que comanda a arquitetura de IA no n8n.
* **Sub-node:** Módulo acoplado ao nó raiz para estender capacidades (modelos, ferramentas, memória).
* **Tool Calling / JSON Schema:** Especificação estruturada enviada ao modelo descrevendo as ferramentas disponíveis e os parâmetros esperados.
* **ReAct Pattern:** Ciclo contínuo de Pensamento (*Thought*), Ação (*Action*) e Observação (*Observation*).
* **$fromAI():** Expressão do n8n que delega ao LLM a extração e preenchimento de variáveis dinâmicas.
* **Max Iterations:** Trava de segurança que interrompe a execução após um limite de chamadas consecutivas para evitar loops infinitos.
* **Human-in-the-loop:** Trava de segurança que exige aprovação manual de um operador antes da execução de ações críticas.

### 4.3 Kit de Prompts Reutilizáveis
1. *"Com base nas fontes, quais são as configurações essenciais no nó de AI Agent para impedir loops infinitos e proteger ações destrutivas em bancos de dados?"*
2. *"Como a clareza da descrição de uma Tool influencia o modelo de linguagem na decisão de qual ferramenta acionar?"*
3. *"Explique o papel do Session ID na segregação de históricos de conversa e descreva o cenário de falha operacional ao usar Simple Memory em produção."*
