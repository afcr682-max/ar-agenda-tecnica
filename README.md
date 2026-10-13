# AR Agenda Técnica — Vercel + Railway + PostgreSQL

Sistema web privado para um administrador: login, cadastro de clientes, agendamentos, painel, WhatsApp com mensagem pré-preenchida, exportação CSV e impressão/salvar em PDF.

## 1. Preparar o GitHub
1. Crie um repositório no GitHub, por exemplo `ar-agenda-tecnica`.
2. Envie o conteúdo desta pasta para o repositório mantendo `backend/` e `frontend/` na raiz.
3. Não envie senhas reais nem um arquivo `.env`.

## 2. Railway: banco e API
1. Crie um projeto na Railway e adicione PostgreSQL.
2. Adicione um serviço Deploy from GitHub Repo apontando para seu repositório.
3. No serviço da API, defina **Root Directory** como `/backend`.
4. Configure as variáveis no serviço da API:
   - `NODE_ENV` = `production`
   - `DATABASE_URL` = referência para a variável `DATABASE_URL` do serviço PostgreSQL da Railway
   - `JWT_SECRET` = chave aleatória de 48+ caracteres. Gere localmente com `node -e "console.log(require('crypto').randomBytes(48).toString('hex'))"`
   - `JWT_EXPIRES_IN` = `8h`
   - `ADMIN_EMAIL` = seu e-mail
   - `ADMIN_PASSWORD` = senha forte e exclusiva
   - `FRONTEND_ORIGIN` = URL final da Vercel, sem barra final
5. A API inicia com `npm start`. Gere um domínio público no serviço da API.
6. Teste `https://SUA-API.up.railway.app/api/health`. A resposta esperada contém `"ok":true`.
7. Na primeira inicialização, o administrador é criado usando `ADMIN_EMAIL` e `ADMIN_PASSWORD`. Alterar essas variáveis depois não redefine a senha de um administrador existente.

## 3. Vercel: frontend
1. Importe o mesmo repositório na Vercel.
2. Configure **Root Directory** como `frontend` e preset `Other` (site estático).
3. Edite `frontend/app.js`: troque a primeira linha `const API_BASE='https://SUA-API.up.railway.app';` pela URL real do serviço Railway.
4. Faça commit/push e aguarde o deploy.
5. Copie a URL final do site e configure `FRONTEND_ORIGIN` na Railway com essa URL exata. Se usar domínio próprio, liste as origens separadas por vírgula.
6. Faça redeploy da API. Acesse a Vercel e entre com o e-mail e senha definidos na Railway.

## 4. Usar o sistema
- Cadastre clientes antes de criar agendamentos.
- Crie agendamentos para instalação de câmeras, manutenção de CFTV, DVR/NVR, rede/Wi-Fi, enlace de rádio, visita técnica ou outro serviço.
- WhatsApp abre uma mensagem pronta; revise e envie manualmente.
- O botão Imprimir / PDF abre a impressão do navegador; selecione “Salvar como PDF”.
- Exporte CSV periodicamente para manter uma cópia dos agendamentos.

## Limitações honestas desta versão
- O sistema usa JWT de 8 horas armazenado em `sessionStorage`; não há cadastro público de usuários.
- WhatsApp não envia mensagens sozinho. O usuário precisa confirmar o envio.
- PDF é gerado pela função de impressão do navegador.
- Lembretes automáticos em segundo plano e com a página fechada ainda não estão implementados nesta versão. Para isso, integrar serviço de notificações e agendador no backend.
- Não existe fluxo de recuperação/troca de senha na interface. Para mudar a senha após criar o administrador, é necessário implementar um fluxo administrativo seguro ou atualizar o hash no banco.
- A API deve ficar atrás de HTTPS e as variáveis secretas só devem ser guardadas na Railway. Faça backups do PostgreSQL e proteja os dados dos clientes.
