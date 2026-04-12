# SGHSS VidaPlus — API Back-end

Este repositório contém a API REST para o sistema SGHSS VidaPlus, um protótipo de plataforma de gestão hospitalar desenvolvido com Node.js, Express e Prisma ORM. A API oferece recursos de autenticação JWT, gerenciamento de pacientes, agendamento e cancelamento de consultas, criação de prontuários, emissão de prescrições, envio de notificações e geração de relatórios. A documentação OpenAPI/Swagger está disponível em `/docs`.

## Stack e requisitos

- Node.js ≥ 18
- PostgreSQL
- Express e Prisma ORM
- JSON Web Tokens (JWT)
- Swagger/OpenAPI (documentação em `/docs`)

## Escopo e módulos

O MVP se concentra em disponibilizar recursos básicos de um sistema hospitalar. Os módulos implementados são:

- **Autenticação** – cadastro de usuários (pacientes, cuidadores, profissionais e administradores), login e consulta do usuário autenticado.
- **Pacientes** – criação de pacientes, listagem com filtros e paginação, visualização por ID, atualização, exclusão e vinculação de cuidadores.
- **Consultas** – agendamento de consultas, listagem com filtros e restrições de perfil, e cancelamento de consultas.
- **Prontuários** – registro da evolução de consultas (incluindo sinais vitais opcionais) e listagem de prontuários por paciente.
- **Prescrições** – emissão de prescrições médicas associadas a prontuários e consulta de prescrições por paciente.
- **Notificações** – envio de notificações (lembretes/orientações) aos pacientes e consulta de notificações por paciente.
- **Relatórios** – geração de relatório de consultas por status e intervalo de datas.
- **Health check** – endpoint simples para verificação de disponibilidade da API.

## Como rodar localmente

1. Instale as dependências:

   ~~~bash
   npm install
   ~~~

2. Copie o arquivo de variáveis de ambiente e ajuste as configurações:

   ~~~bash
   cp .env.example .env
   # edite DATABASE_URL (string de conexão do PostgreSQL) e JWT_SECRET
   ~~~

3. Gere o cliente do Prisma ORM e aplique as migrações no banco de dados:

   ~~~bash
   npx prisma generate
   npx prisma migrate dev
   ~~~

4. Inicie o servidor em modo de desenvolvimento (utiliza nodemon):

   ~~~bash
   npm run dev
   ~~~

   Por padrão a API escuta na porta `3000`. Você pode ajustar a porta definindo a variável `PORT` no `.env`.

## Principais endpoints

- **Health check**:  
  `GET /health` – retorna `{ status: "ok" }`.

- **Documentação**:  
  `GET /docs` – interface Swagger/OpenAPI com todos os detalhes dos endpoints.

- **Autenticação**:
  - `POST /auth/signup` – cadastra um novo usuário (alias: `/auth/register`).
  - `POST /auth/login` – realiza login e devolve um token JWT.
  - `GET /auth/me` – retorna os dados do usuário autenticado.

- **Pacientes**:
  - `POST /pacientes` – cria um novo paciente.
  - `GET /pacientes` – lista pacientes (com paginação e busca por nome/CPF).
  - `GET /pacientes/:id` – obtém detalhes de um paciente pelo ID (com restrições de acesso).
  - `PUT /pacientes/:id` – atualiza dados de um paciente.
  - `DELETE /pacientes/:id` – remove um paciente.
  - `POST /pacientes/:id/vinculos` – vincula um cuidador a um paciente.

- **Consultas**:
  - `POST /consultas` – agenda uma consulta.
  - `GET /consultas` – lista consultas com filtros de paciente, profissional e status (as restrições variam conforme o perfil do usuário).
  - `PATCH /consultas/:id/cancelar` – cancela uma consulta e registra o motivo.

- **Prontuários**:
  - `POST /prontuarios` – cria um prontuário ligado a uma consulta (somente profissionais).
  - `GET /prontuarios/paciente/:pacienteId` – lista prontuários de um paciente (inclui dados da consulta e prescrições associadas).

- **Prescrições**:
  - `POST /prescricoes` – emite uma prescrição associada a um prontuário (somente profissionais).
  - `GET /prescricoes/paciente/:pacienteId` – lista prescrições de um paciente.

- **Notificações**:
  - `POST /notificacoes` – envia uma notificação para um paciente.
  - `GET /notificacoes/paciente/:pacienteId` – consulta notificações recebidas pelo paciente.

- **Relatórios**:
  - `GET /relatorios/consultas` – retorna resumo de consultas por status em um intervalo opcional de datas (somente administradores).

Observação: Apenas as rotas com prefixo `/api/auth/*` e `/api/pacientes/*` são aliases de `/auth/*` e `/pacientes/*`, respectivamente.
