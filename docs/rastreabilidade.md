# Rastreabilidade de Requisitos

Este documento estabelece a ligacao entre os requisitos funcionais da API do **SGHSS VidaPlus** e as implementacoes presentes no repositorio. Ele relaciona cada requisito aos endpoints correspondentes, indica se ha evidencia de caso de teste (CT) formal versionado e descreve brevemente o estado de implementacao. Quando nao existem casos de teste formais no repositorio, isso e indicado explicitamente.

## Tabela de rastreabilidade

| Requisito | Descricao resumida | Endpoint(s) relacionado(s) | Evidencia/CT formal | Status | Observacao |
| --- | --- | --- | --- | --- | --- |
| **REQ-01** | Health check | `GET /health` | testes_seguros.sh executa `GET /health` | Implementado | Endpoint acessivel sem autenticacao, definido na API. |
| **REQ-02** | Autenticacao: cadastro e login | `POST /auth/signup`, `POST /auth/register` (alias), `POST /auth/login`, `GET /auth/me` | testes_seguros.sh cobre cadastro, login e consulta do usuario | Implementado | `POST /auth/register` e alias e esta marcado como depreciado no OpenAPI. |
| **REQ-03** | Gerenciamento de pacientes | `POST /pacientes`, `GET /pacientes`, `GET /pacientes/{id}`, `PUT /pacientes/{id}`, `DELETE /pacientes/{id}`, `POST /pacientes/{id}/vinculos` | testes_seguros.sh cobre listagem e criacao de pacientes; demais operacoes nao tem CT formal versionado | Implementado | Endpoints de atualizacao, exclusao e vinculo existem, mas nao ha CT formal versionado para essas acoes. |
| **REQ-04** | Consultas: agendar, listar e cancelar | `POST /consultas`, `GET /consultas`, `PATCH /consultas/{id}/cancelar` | sem CT formal versionado | Implementado | Operacoes definidas no OpenAPI; nao ha testes no repositorio. |
| **REQ-05** | Prontuarios (medical records) | `POST /prontuarios`, `GET /prontuarios/paciente/{pacienteId}` | sem CT formal versionado | Implementado | Apenas profissionais podem criar e ha restricoes de acesso na listagem. |
| **REQ-06** | Prescricoes | `POST /prescricoes`, `GET /prescricoes/paciente/{pacienteId}` | sem CT formal versionado | Implementado | Criacao restrita a profissionais e listagem por paciente. |
| **REQ-07** | Notificacoes | `POST /notificacoes`, `GET /notificacoes/paciente/{pacienteId}` | sem CT formal versionado | Implementado | Envio de notificacoes restrito a administradores/profissionais; listagem restrita por perfil. |
| **REQ-08** | Relatorios de consultas | `GET /relatorios/consultas` | sem CT formal versionado | Implementado | Gera relatorio de consultas por status e periodo, acessivel apenas a administradores. |

## Limitacoes e observacoes

* **Falta de casos de teste formais:** O repositorio contem apenas o script `testes_seguros.sh`, que executa um conjunto reduzido de verificacoes (health check, cadastro e login de usuario, listagem e criacao de pacientes, e verificacao de acesso nao autenticado). Nao ha testes versionados para consultas, prontuarios, prescricoes, notificacoes ou relatorios. Assim, a validacao dessas funcionalidades depende unicamente da inspecao do codigo e da documentacao.
* **Aliases de rota:** Conforme definido em `src/app.js`, as rotas de autenticacao e pacientes possuem aliases com o prefixo `/api`. Por exemplo, `/api/auth/signup` e equivalente a `/auth/signup` e `/api/pacientes` e equivalente a `/pacientes`. Os demais modulos (consultas, prontuarios, prescricoes, notificacoes e relatorios) nao possuem alias `/api` no codigo atual.
* **Escopo atual vs. melhorias futuras:** Todos os requisitos listados acima estao implementados segundo o codigo-fonte e a documentacao OpenAPI. Eventuais melhorias futuras (por exemplo, dashboards, relatorios adicionais ou integracoes externas) nao estao representadas aqui porque nao existem implementacoes ou especificacoes formais no repositorio.
* **Numeracao de requisitos:** Como o repositorio nao define uma numeracao oficial para requisitos, foram utilizados identificadores genericos `REQ-01`, `REQ-02`, etc., para fins de rastreabilidade. Esses identificadores podem ser ajustados conforme a documentacao oficial do projeto.
