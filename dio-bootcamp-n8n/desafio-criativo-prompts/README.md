
##  Desafio Criativo: Planejando Automações com n8n Usando Bons Prompts

Planejamento arquitetural completo de um fluxo automatizado no n8n para cadastrar contatos via formulário web e enviar boletins meteorológicos personalizados via WhatsApp.

---

###  Passo 1: Definição da Automação

Quero criar uma automação no N8N para cadastrar usuários via formulário web e disparar alertas meteorológicos diários e personalizados via WhatsApp com base na localização informada.

* **Público ou responsável:**  
  Pessoas cadastradas que desejam planejar o dia com base na previsão matinal do tempo (levar guarda-chuva ou aproveitar o dia de sol).

* **Resultado esperado:**  
  Validar os dados de entrada, registrar o usuário em uma planilha organizada por cidade, enviar uma mensagem imediata de boas-vindas/confirmação no WhatsApp e programar a rotina matinal de envio do clima.

---

###  Passo 2: Contexto e Regras Operacionais

* **Ferramentas envolvidas:**  
  Google Forms (ou n8n Form Trigger), Google Sheets, Open-Meteo API (via nó HTTP Request) e API de WhatsApp (Evolution API / Z-API).

* **Fluxo desejado:**  
  1. **Captura:** Receber os dados preenchidos no formulário (Nome, Cidade e WhatsApp).
  2. **Validação:** Verificar se o número de WhatsApp é válido (DDI + DDD + 9 dígitos) e se os campos obrigatórios estão preenchidos.
  3. **Persistência:** Registrar o novo assinante na planilha do Google Sheets na aba correspondente à sua cidade/região.
  4. **Boas-vindas:** Enviar mensagem instantânea de confirmação no WhatsApp com a previsão do dia atual.
  5. **Rotina Recorrente (Agendador):** Disparar diariamente às 07h00 da manhã um lote de mensagens consultando a previsão em tempo real para cada cidade cadastrada.

* **Regras importantes:**  
  * Rejeitar ou interromper a execução para registros cujo número de telefone não contenha o formato numérico padrão com DDD.
  * Tratar exceções de cidades com nomes grafados incorretamente ou acentuação divergente antes de chamar a API de clima.
  * Mensagem condicional: se a probabilidade de precipitação for maior que 40%, incluir o alerta enfático de guarda-chuva; caso contrário, recomendar aproveitar o dia aberto/ensolarado.

---

###  Passo 3: O Prompt Final (Instrução Mestre)

```text
Atue como um especialista em N8N.
Crie uma automação para enviar a previsão do tempo personalizada via WhatsApp para contatos recebidos por formulário.

Público:
Pessoas cadastradas que desejam receber um boletim meteorológico matinal e avisos práticos para o seu dia.

Ferramentas envolvidas:
Google Forms (ou n8n Form Trigger), Google Sheets, Open-Meteo API (via nó HTTP Request) e API de WhatsApp (Evolution API ou Z-API).

Fluxo:
1. Receber os dados do formulário (Nome, Cidade e WhatsApp).
2. Validar e sanitizar os dados (verificar se o número de telefone é válido).
3. Salvar o contato na planilha do Google Sheets.
4. Consultar a API de clima para buscar temperatura e probabilidade de chuva da cidade informada.
5. Montar a mensagem personalizada (alertando sobre chuva ou sugerindo aproveitar o sol).
6. Disparar a mensagem de confirmação e clima no WhatsApp do usuário.

Regras:
- Validar se o telefone contém o formato correto com DDD; se for inválido, interromper a execução antes de salvar ou disparar mensagem.
- Tratar retornos vazios ou erros de localização da API de previsão sem travar a automação.
- Estruturar a lógica condicional para definir se o alerta de guarda-chuva deve ser ativado.

Explique quais nós do N8N devem ser utilizados e a lógica de funcionamento do workflow.
```

---

###  Arquitetura de Nós no n8n e Lógica de Funcionamento

Para implementar essa automação de ponta a ponta, o workflow no n8n é composto pelos seguintes nós conectados em sequência:

| Etapa | Nó do n8n | Tipo / Operação | Função Técnica |
| :--- | :--- | :--- | :--- |
| **1. Gatilho** | `n8n Form Trigger` | Trigger | Exibe o formulário web e escuta os envios em tempo real (`nome`, `cidade`, `whatsapp`). |
| **2. Validação** | `If Node` / `Code` | Core Logic | Valida se o telefone possui formato numérico válido com DDD antes de seguir o fluxo. |
| **3. Persistência** | `Google Sheets` | App Node (`Append Row`) | Salva os dados do lead em uma planilha para controle e futuros disparos em lote. |
| **4. Consulta Clima** | `HTTP Request` | Core Action (`GET`) | Faz requisição à API meteorológica (Open-Meteo) com a cidade para puxar temperatura e chance de chuva. |
| **5. Decisão** | `If Node` | Core Logic | Avalia a probabilidade de precipitação: se `chuva > 40%`, segue para o ramo de chuva; senão, ramo de sol. |
| **6. Mensagem** | `Edit Fields (Set)` | Data Transform | Monta a mensagem personalizada interpolando o nome do contato, clima atual e a dica do dia. |
| **7. Disparo** | `HTTP Request` | Core Action (`POST`) | Envia a mensagem gerada para a API do WhatsApp (Evolution API / Z-API), entregando na conversa do usuário. |

---
