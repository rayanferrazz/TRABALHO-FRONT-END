# Organizador de Tarefas — FastAPI + Front-end Web

![FastAPI](https://img.shields.io/badge/FastAPI-009688?logo=fastapi&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?logo=sqlite&logoColor=white)
![JWT](https://img.shields.io/badge/Auth-JWT-black?logo=jsonwebtokens&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)

Aplicação full-stack de **lista de tarefas (to-do list)** com autenticação, desenvolvida como trabalho final de **Front-end** da Residência de Software do **Serratec**.

O projeto é dividido em:

- **Backend** (`server/`): API REST em **FastAPI + SQLAlchemy + SQLite**, com autenticação **JWT** e senhas com **bcrypt**;
- **Frontend** (`client/`): páginas HTML/CSS/JavaScript que consomem a API, com login, cadastro e CRUD de tarefas.

> 🎥 Vídeo explicativo do projeto: [Google Drive](https://drive.google.com/file/d/11igEzgSwqu-5II8OKD68e2K1FIuIhlgq/view?usp=sharing)

---

## Sumário

- [Funcionalidades](#funcionalidades)
- [Arquitetura](#arquitetura)
- [Tecnologias](#tecnologias)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Pré-requisitos](#pré-requisitos)
- [Como executar o backend](#como-executar-o-backend)
- [Como executar o frontend](#como-executar-o-frontend)
- [Endpoints da API](#endpoints-da-api)
- [Exemplos de requisição](#exemplos-de-requisição)
- [Autenticação](#autenticação)
- [Testes](#testes)
- [Banco de dados](#banco-de-dados)
- [Estado atual e problemas conhecidos](#estado-atual-e-problemas-conhecidos)
- [Equipe](#equipe)
- [Melhorias sugeridas](#melhorias-sugeridas)

---

## Funcionalidades

### Backend

- Cadastro de usuários com validação de e-mail (`EmailStr`);
- Hash de senha com **bcrypt**;
- Login com emissão de **JWT** (HS256, validade de 24 horas);
- Endpoint `/auth/me` para obter o usuário autenticado;
- CRUD de tarefas **por usuário**;
- Autorização: cada usuário só acessa suas próprias tarefas (`403` caso contrário);
- Validações com Pydantic;
- Tratamento de erros com `HTTPException`;
- Documentação automática (Swagger/ReDoc);
- Testes com **pytest** + **TestClient** (SQLite em memória).

### Frontend

- Tela de **login** e **cadastro** na mesma página;
- Armazenamento do token no `localStorage`;
- Opção “Lembrar senha” (salva apenas o e-mail);
- Redirecionamento para a página de tarefas;
- Listagem de tarefas;
- Criação, edição e remoção de tarefas;
- Logout.

## Arquitetura

```text
┌────────────────────┐        HTTP/JSON        ┌──────────────────────┐
│  client/           │  ───────────────────▶   │  server/             │
│  HTML + CSS + JS   │  Authorization: Bearer  │  FastAPI + SQLAlchemy│
│  localStorage      │  ◀───────────────────   │  SQLite + JWT        │
└────────────────────┘                         └──────────────────────┘
```

## Tecnologias

| Camada | Tecnologia |
| --- | --- |
| Backend | Python 3.10+, FastAPI, Uvicorn |
| ORM | SQLAlchemy 2 |
| Validação | Pydantic v2 + `email-validator` |
| Banco | SQLite (`server/database/db.sqlite`) |
| Autenticação | JWT (`python-jose`) + bcrypt |
| Testes | pytest + FastAPI TestClient |
| Frontend | HTML5, CSS3, JavaScript (Vanilla) |
| Ícones | Boxicons (CDN) |

## Estrutura do projeto

```text
.
├── client/
│   ├── index.html          # Login e cadastro
│   ├── home.html           # Lista de tarefas
│   ├── scripts.js          # Login/cadastro
│   ├── home.js             # CRUD de tarefas
│   └── style.css
└── server/
    ├── main.py             # Aplicação FastAPI e rotas
    ├── security.py         # Hash, JWT e verificação
    ├── database/
    │   ├── __init__.py     # Engine, sessão e init_db
    │   └── db.sqlite       # Banco SQLite (gerado)
    ├── entities/
    │   ├── user.py         # Modelo User
    │   └── task.py         # Modelo Task
    ├── schemas/
    │   ├── auth.py
    │   ├── common.py
    │   ├── task.py
    │   └── user.py
    ├── usecases/
    │   ├── users.py        # Regras de usuário
    │   └── tasks.py        # Regras de tarefa
    ├── tests/
    │   ├── conftest.py
    │   ├── test_auth.py
    │   └── test_tasks.py
    ├── requirements.txt
    ├── scripts.sh
    └── README.md           # README específico do backend
```

## Pré-requisitos

- **Python 3.10+**;
- **pip**;
- Navegador moderno;
- Git Bash (opcional, para usar `scripts.sh` no Windows).

## Como executar o backend

### Opção 1 — Manual (recomendado)

```bash
cd server
python -m venv venv
```

Ative o ambiente virtual:

```bash
# Windows (PowerShell)
.\venv\Scripts\Activate.ps1

# Windows (CMD)
venv\Scripts\activate.bat

# Linux/macOS
source venv/bin/activate
```

Instale as dependências:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Execute:

```bash
uvicorn main:app --reload
```

A API estará em `http://127.0.0.1:8000`.

- Swagger: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`
- Health/redirect: `http://127.0.0.1:8000/`

### Opção 2 — Script automatizado (Git Bash/WSL)

```bash
cd server
chmod +x scripts.sh
./scripts.sh setup
./scripts.sh dev
```

Comandos disponíveis:

| Comando | Ação |
| --- | --- |
| `./scripts.sh setup` | Cria a venv e instala dependências |
| `./scripts.sh dev` | Sobe o Uvicorn com reload |
| `./scripts.sh test-unit` | Executa o pytest |
| `./scripts.sh clean` | Remove caches e arquivos temporários |
| `./scripts.sh help` | Exibe a ajuda |

> No Windows, o script ativa `venv/Scripts/activate`; por isso deve ser executado no **Git Bash** ou **WSL**, não no PowerShell.

## Como executar o frontend

Com o backend rodando, sirva a pasta `client`:

```bash
cd client
python -m http.server 5500
```

Acesse `http://localhost:5500`.

Também é possível usar a extensão **Live Server** do VS Code (abra `client/index.html`).

> A URL da API está fixa em `http://127.0.0.1:8000` nos arquivos `scripts.js` e `home.js`. Se o backend rodar em outra porta/host, ajuste a constante `API_BASE_URL`.

## Endpoints da API

Base: `http://127.0.0.1:8000`

### Autenticação

| Método | Rota | Descrição | Auth |
| --- | --- | --- | --- |
| `POST` | `/auth/signup` | Cadastra usuário e retorna token | ❌ |
| `POST` | `/auth/login` | Autentica e retorna token | ❌ |
| `GET` | `/auth/me` | Retorna o usuário autenticado | ✅ Bearer |

### Tarefas

| Método | Rota | Descrição | Auth |
| --- | --- | --- | --- |
| `POST` | `/tasks` | Cria tarefa | ✅ |
| `GET` | `/tasks` | Lista tarefas do usuário | ✅ |
| `GET` | `/tasks/{id}` | Busca tarefa por ID | ✅ |
| `PUT` | `/tasks/{id}` | Atualiza tarefa | ✅ |
| `DELETE` | `/tasks/{id}` | Remove tarefa | ✅ |

## Exemplos de requisição

### Cadastro

```http
POST /auth/signup
Content-Type: application/json
```

```json
{
  "name": "Rayan Ferraz",
  "email": "rayan@email.com",
  "password": "senha123"
}
```

Resposta `201 Created`:

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "token_type": "bearer",
  "user": {
    "id": 1,
    "name": "Rayan Ferraz",
    "email": "rayan@email.com"
  }
}
```

### Login

```http
POST /auth/login
Content-Type: application/json
```

```json
{
  "email": "rayan@email.com",
  "password": "senha123"
}
```

### Criar tarefa

```http
POST /tasks
Authorization: Bearer <token>
Content-Type: application/json
```

```json
{
  "title": "Estudar FastAPI",
  "description": "Revisar autenticação JWT",
  "deadline": "2026-12-31"
}
```

Resposta `201 Created`:

```json
{
  "title": "Estudar FastAPI",
  "description": "Revisar autenticação JWT",
  "deadline": "2026-12-31",
  "id": 1,
  "user_id": 1,
  "created_at": "2026-11-16T10:00:00"
}
```

### Atualizar tarefa

```http
PUT /tasks/1
Authorization: Bearer <token>
Content-Type: application/json
```

```json
{
  "title": "Estudar FastAPI e SQLAlchemy"
}
```

### Listar tarefas

```http
GET /tasks
Authorization: Bearer <token>
```

Resposta: `200 OK` com um **array** de tarefas.

## Autenticação

1. O cadastro/login retorna um **JWT** assinado com HS256;
2. O frontend salva o token em `localStorage` (`authToken`);
3. As requisições enviam `Authorization: Bearer <token>`;
4. O backend decodifica o token, extrai o `sub` (ID do usuário) e busca o usuário no banco;
5. Sem token ou com token inválido → `401 Unauthorized`;
6. Tarefa de outro usuário → `403 Forbidden`.

A chave secreta é lida de `TODO_SECRET_KEY`:

```python
SECRET_KEY = os.getenv("TODO_SECRET_KEY", "super-secret-key-change-me")
```

> ⚠️ O valor padrão é inseguro. Defina `TODO_SECRET_KEY` em produção:
>
> ```bash
> export TODO_SECRET_KEY="uma-chave-longa-e-aleatoria"
> ```

## Testes

```bash
cd server
pytest tests/ -v
```

Os testes usam um banco SQLite **em memória** com `StaticPool` e recriam o schema antes de cada teste.

> ⚠️ Os testes de tarefas (`tests/test_tasks.py`) esperam uma resposta no formato `{"tasks": [...]}`, mas o endpoint retorna um **array** diretamente. Por isso, esses testes precisam ser atualizados para refletir o contrato atual da API.

## Banco de dados

- Banco: **SQLite** em `server/database/db.sqlite`;
- Criado automaticamente no startup (`init_db`);
- O arquivo `db.sqlite` está versionado no repositório — em um projeto real, ele não deveria ser commitado (adicione ao `.gitignore`).

**Tabelas**

| Tabela | Campos |
| --- | --- |
| `users` | `id`, `name`, `email` (único), `password` (hash bcrypt) |
| `tasks` | `id`, `title`, `description`, `deadline`, `created_at`, `owner_id` |

## Estado atual e problemas conhecidos

O backend está funcional para autenticação e CRUD de tarefas, mas o frontend e o backend **não estão totalmente alinhados**:

1. **Campo `completed` não existe no backend**
   - O frontend envia `completed` ao criar/atualizar tarefas, mas o schema `Task` não possui esse campo. O valor é ignorado.
   - O checkbox “concluir tarefa” chama `PATCH /tasks/{id}`, mas a API só implementa `PUT`. Resultado: **405 Method Not Allowed**.
2. **Formato da listagem**
   - `GET /tasks` retorna um array; os testes esperam `{"tasks": []}`.
3. **Edição de tarefa**
   - O frontend usa `PUT` para editar o título, o que funciona.
4. **Mensagens de retorno**
   - O frontend procura um elemento `#messageArea` que não existe no `home.html`; mensagens de sucesso/erro não são exibidas.
5. **“Lembrar senha”**
   - Salva apenas o e-mail, não a senha (comportamento correto e seguro).
6. **CORS**
   - `allow_origins=["*"]` com `allow_credentials=True` é permissivo; restrinja em produção.
7. **Segredos**
   - `TODO_SECRET_KEY` tem valor padrão fraco.
8. **Arquivos gerados commitados**
   - `db.sqlite` e pastas `__pycache__` estão no repositório.
9. **Dependências sem versão fixa**
   - `requirements.txt` não fixa versões, o que pode quebrar builds futuros.

## Equipe

| Nome | Matrícula |
| --- | --- |
| Douglas Alves | 2024200188 |
| Nicolas Marconi | 2024200320 |
| Rayan Ferraz | 2024200152 |
| Rayane Cordeiro | 2024200023 |

## Melhorias sugeridas

- Adicionar `completed` ao modelo/schema e implementar `PATCH /tasks/{id}` (ou ajustar o frontend para `PUT`);
- Padronizar a listagem (`array` vs. objeto com `tasks`);
- Criar o elemento `#messageArea` no HTML ou remover o código que o referencia;
- Adicionar `.gitignore` para `db.sqlite`, `__pycache__`, `venv` e caches;
- Fixar versões no `requirements.txt` (ou usar `pyproject.toml`/Poetry);
- Externalizar `TODO_SECRET_KEY` e restringir CORS;
- Adicionar migrações com Alembic;
- Criar testes de autorização (acesso a tarefa de outro usuário);
- Melhorar a interface com estados de carregamento e confirmações;
- Adicionar CI (GitHub Actions) rodando `pytest`.

## Contexto acadêmico

Trabalho final da disciplina de **Front-end** — Residência de Software **Serratec**.

## Autor

Desenvolvido por **Douglas Alves, Nicolas Marconi, Rayan Ferraz e Rayane Cordeiro**.

- GitHub: [@rayanferrazz](https://github.com/rayanferrazz)
