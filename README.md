# ESTOQUE 360 — Web App

Frontend responsivo para GitHub Pages + backend Google Apps Script ligado à planilha atual.

## O que já está implementado

- Baixa por OS: até 15 materiais.
- Baixa por Tarefa: até 20 materiais.
- Quantidade por item.
- Campo "Quem vai receber o material".
- Busca de material por código, descrição, marca ou família.
- Exibição do estoque atual.
- Devolução por OS ou Tarefa.
- A devolução calcula: retirado, já devolvido e disponível para devolver.
- Registro de histórico das baixas e devoluções.
- Atualização direta da coluna ESTOQUE!I (ESTOQUE ATUAL).
- Compatibilidade com as abas de saída existentes.
- Comando de voz para reconhecer comandos como:
  - "Baixa OS 3952"
  - "OS três nove cinco dois"
  - "Tarefa sete oito dois um"
  - "Devolução OS 3952"
- Interface mobile-first inspirada na tela enviada.

## Arquitetura

GitHub Pages
  ↓
index.html
  ↓
Google Apps Script Web App
  ↓
Google Sheets

A planilha continua sendo a base de dados. O Web App não cria uma segunda base de estoque.

## 1. Instalar o backend

Abra a planilha de estoque atual.

Google Sheets → Extensões → Apps Script.

Crie/adicione um arquivo `Code.gs` e cole o conteúdo deste projeto.

Importante: o projeto Apps Script deve estar vinculado à mesma planilha que contém a aba `ESTOQUE`.

O backend cria automaticamente, na primeira operação:

- `HISTÓRICO SAÍDAS APP`
- `HISTÓRICO DEVOLUÇÕES APP`
- `TRANSAÇÕES APP`

As abas existentes não precisam ser apagadas.

## 2. Publicar o backend

No Apps Script:

Implantar → Nova implantação → Aplicativo da Web.

Sugestão inicial para uso interno:
- Executar como: você
- Quem tem acesso: usuários autorizados da organização/conta Google

Se a empresa precisar que o app funcione sem login, a implantação pode ser pública, mas isso exige uma camada adicional de autenticação/token antes de colocar o app em produção.

Copie a URL `/exec`.

## 3. Configurar o frontend

Abra `index.html`.

Procure:

const API_URL = 'COLE_AQUI_A_URL_DO_WEB_APP_DO_APPS_SCRIPT';

Substitua pelo endereço `/exec` copiado no passo anterior.

Exemplo:

const API_URL = 'https://script.google.com/macros/s/SEU_ID/exec';

## 4. Publicar no GitHub

Crie um repositório, por exemplo:

estoque360-webapp

Envie:

- `index.html`
- `Code.gs`
- `README.md`

No GitHub:

Settings → Pages → Deploy from branch → main → /root.

O endereço ficará parecido com:

https://SEU-USUARIO.github.io/estoque360-webapp/

## 5. Estrutura da planilha

A implementação considera a estrutura atual:

ESTOQUE:
- E = Código interno
- F = Descrição
- I = Estoque atual

As saídas atuais usam:
- C = Código
- D = Descrição
- E = Quantidade
- F = Unidade
- G = Responsável
- H = OS/TAREFA/OUTROS
- I = Observações
- J = Baixa no estoque

O script atual confirma essa estrutura. O Web App mantém essa compatibilidade e adiciona um histórico próprio para as operações feitas pelo app.

## 6. Importante sobre devolução

As saídas antigas da planilha podem ser localizadas pelo número da OS/Tarefa.

As devoluções feitas pelo novo Web App passam a ser registradas em `HISTÓRICO DEVOLUÇÕES APP`, permitindo controlar devoluções parciais.

Exemplo:

Retirado: 25 kg
Já devolvido: 3 kg
Disponível para devolver: 22 kg

Isso evita devolver mais material do que foi retirado.

## 7. Comando de voz

O recurso usa a Web Speech API do navegador.

A melhor compatibilidade é Chrome/Android.

O comando não precisa ser exatamente igual ao exemplo. O app tenta identificar:

- OS
- Tarefa
- Devolução
- número falado em dígitos

Exemplo:

"OS três nove cinco dois"

→ OS 3952

"Devolução OS 3952"

→ abre a consulta de devolução da OS 3952.

## 8. Observação sobre comunicação GitHub ↔ Apps Script

O projeto usa JSONP para consultas e envio de gravações por formulário oculto, em vez de depender exclusivamente de `fetch()` cross-origin.

Isso foi escolhido porque Web Apps do Apps Script podem apresentar limitações de CORS/redirect quando um frontend externo, como GitHub Pages, tenta consumir diretamente a resposta do Apps Script.

## Próxima evolução recomendada

Depois de testar esta primeira versão, podemos acrescentar:

1. Autenticação por usuário.
2. Lista de colaboradores para o campo "Quem vai receber".
3. Tela de OS/Tarefas recentes.
4. Histórico visual por OS/Tarefa.
5. Scanner de código de barras/QR Code.
6. Bloqueio ou alerta quando a quantidade solicitada supera o estoque.
7. Integração com o InforOS.
8. Registro de assinatura digital do recebedor.
