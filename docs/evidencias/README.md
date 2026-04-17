# Evidências de Testes e Validação

Este documento compila as evidências de testes e validação que estão **efetivamente versionadas** no repositório do projeto **SGHSS VidaPlus**.  Seu objetivo é deixar claro o que foi testado, o que não foi e quais scripts/automações existem, reduzindo o risco de inferir uma cobertura que não existe.

## Evidências versionadas no repositório

- **`testes_seguros.sh`** – script de shell localizado na raiz do projeto.  É a única peça de automação de testes versionada.  Esse script configura variáveis de ambiente, gera o cliente Prisma, aplica as migrações e executa uma série de requisições `curl` para verificar alguns endpoints básicos.  Ele grava as respostas em arquivos dentro da pasta `docs/evidencias` e gera um relatório em `docs/RELATORIO-TESTES.md` se a variável `PUSH_EVIDENCES` estiver ajustada.  Por padrão, o relatório e as evidências não são comitados automaticamente.
- **Workflow `testes_seguros.yml`** – arquivo em `.github/workflows` que executa o script `testes_seguros.sh` em um container GitHub Actions.  O workflow instala dependências, gera o cliente Prisma, aplica as migrações e roda o script.  Ele **não** persiste nem publica os resultados dos testes; serve apenas para garantir que o script possa ser executado em ambiente de CI.
- **Documentação e rastreabilidade** – os arquivos `README.md` e `docs/rastreabilidade.md` descrevem o escopo das funcionalidades e mapeiam requisitos para endpoints, mas **não** são evidências de teste por si só.
- **Ausência de relatórios e logs** – não existe no repositório nenhum `RELATORIO-TESTES.md` versionado nem arquivos de log resultantes da execução de testes.  A pasta `docs/evidencias` é criada dinamicamente pelo script e, até o momento, contém apenas este `README.md`.

## O que estas evidências realmente cobrem

O script `testes_seguros.sh` executa um conjunto reduzido de verificações:

1. **Health check** – envia um `GET /health` para verificar se o servidor está no ar e responde com status 200.
2. **Registro de usuário administrador** – faz um `POST /api/auth/register` (alias de `/auth/signup`) com dados de um usuário do tipo ADMIN, verificando se o cadastro retorna 201 (ou 409 se o usuário já existir).
3. **Autenticação** – envia um `POST /api/auth/login` para realizar login; se bem-sucedido, obtém um token JWT.
4. **Consulta de usuário autenticado** – com o token em mãos, executa `GET /api/auth/me` para recuperar as informações do usuário logado.
5. **Listagem de pacientes** – com o mesmo token, faz um `GET /api/pacientes` para listar pacientes registrados.
6. **Criação de paciente** – envia um `POST /api/pacientes` com dados de um paciente e verifica se o cadastro retorna 201.
7. **Requisição não autenticada** – realiza um `GET /api/pacientes` sem token para confirmar que a API responde com 401 (não autorizado).

Essas verificações cobrem apenas os endpoints listados acima e testam principalmente o fluxo de autenticação e o gerenciamento básico de pacientes (listar e criar).  O script não verifica os conteúdos das respostas além dos códigos de status, não testa cenários de atualização, exclusão ou vinculação de pacientes, nem cobre outros domínios do sistema.

## Limitações atuais das evidências

- **Cobertura limitada**: apenas os endpoints de health, cadastro/login, consulta do usuário autenticado e listagem/criação de pacientes são exercitados.  Não há testes para atualizações ou exclusões de pacientes, vinculação de cuidadores, agendamento de consultas, prontuários, prescrições, notificações ou relatórios.
- **Sem relatório versionado**: embora o script gere arquivos de evidência e um relatório (`docs/RELATORIO-TESTES.md`), esses arquivos não são versionados no repositório por padrão; portanto, não há prova formal dos resultados dos testes.
- **Ausência de logs de CI**: o workflow do GitHub Actions executa o script, mas não armazena logs ou artefatos de execução.  Assim, não é possível rastrear a saída do pipeline via repositório.
- **Sem testes unitários ou de cobertura**: não há testes escritos em frameworks de testes JavaScript/TypeScript (como Jest ou Mocha), nem métricas de cobertura de código.
- **Dependência de configuração local**: o script pressupõe que as migrações podem ser rodadas e que o ambiente está devidamente configurado, o que pode causar variabilidade nos resultados se executado em contextos diferentes.

## O que está implementado versus evidenciado

| Estado | Módulos e operações |
| --- | --- |
| **Implementado com alguma evidência versionada** | Health check (`GET /health`), cadastro/login de usuários e consulta do usuário (`POST /auth/signup` / `POST /auth/register`, `POST /auth/login`, `GET /auth/me`), listagem e criação de pacientes (`GET /pacientes`, `POST /pacientes`). Essas funcionalidades têm um roteiro de teste no script `testes_seguros.sh` e podem ser parcialmente validadas pela execução manual do script. |
| **Implementado sem CT formal versionado** | Atualização e exclusão de pacientes (`PUT /pacientes/{id}`, `DELETE /pacientes/{id}`), vinculação de cuidadores (`POST /pacientes/{id}/vinculos`), agendamento de consultas e cancelamento (`POST /consultas`, `GET /consultas`, `PATCH /consultas/{id}/cancelar`), prontuários (`POST /prontuarios`, `GET /prontuarios/paciente/{pacienteId}`), prescrições (`POST /prescricoes`, `GET /prescricoes/paciente/{pacienteId}`), notificações (`POST /notificacoes`, `GET /notificacoes/paciente/{pacienteId}`) e relatórios (`GET /relatorios/consultas`). Esses módulos estão implementados no código e documentados no OpenAPI e no README, mas não possuem casos de teste formais versionados nem logs de execução armazenados. |
| **Melhoria futura ou evidência ausente** | Qualquer forma de teste automatizado mais robusto (unitário, integração, E2E) e artefatos resultantes como relatórios de cobertura, prints de execução ou coleção do Postman. Também não há evidência da execução do workflow de testes no GitHub Actions (logs ou artifacts). |

---

Este arquivo será atualizado conforme novas evidências de testes forem adicionadas ou conforme a automação de CI/CD passe a registrar e versionar os resultados das execuções.
