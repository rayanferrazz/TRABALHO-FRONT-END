# Todo List API — Backend (FastAPI)

Backend da aplicação **Organizador de Tarefas**, construído com **FastAPI**, **SQLAlchemy**, **SQLite** e autenticação **JWT**.

> 📖 Documentação completa do projeto (backend + frontend): [README principal](../README.md)

---

## Sumário

- [Requisitos](#requisitos)
- [Como rodar](#como-rodar)
- [Variáveis de ambiente](#variáveis-de-ambiente)
- [Endpoints](#endpoints)
- [Estrutura](#estrutura)
- [Testes](#testes)
- [Observações](#observações)

---

## Requisitos

- Python 3.10+
- pip

## Como rodar

```bash
cd server
python -m venv venv
```

Ative a venv:

```bash
# Windows (PowerShell)
.\venv\Scripts\Activate.ps1

# Windows (CMD)
venv\Scripts\activate.bat

# Linux/macOS
source venv/bin/activate
```

Instale e execute:

```bash
pip install -r requirements.txt
uvicorn main:app --reload
```

A API estará em `http://127.0.0.1:8000`.

- Swagger UI: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`

### Script auxiliar (Git Bash/WSL)

```bash
chmod +x scripts.sh
./scripts.sh setup
./scripts.sh dev
```

| Comando | Ação |
| --- | --- |
| `./scripts.sh setup` | Cria a venv e instala dependências |
| `./scripts.sh dev` | Sobe o Uvicorn com reload |
| `./scripts.sh test-unit` | Executa o pytest |
| `./scripts.sh clean` | Remove caches temporários |
| `./scripts.sh help` | Mostra a ajuda |

## Variáveis de ambiente

| Variável | Padrão | Descrição |
| --- | --- | --- |
| `TODO_SECRET_KEY` | `super-secret-key-change-me` | Chave de assinatura do JWT |

> ⚠️ Em produção, sempre defina uma chave forte e aleatória.

## Endpoints

### Autenticação

| Método | Rota | Descrição |
| --- | --- | --- |
| `POST` | `/auth/signup` | Cadastro + token |
| `POST` | `/auth/login` | Login + token |
| `GET` | `/auth/me` | Usuário autenticado |

### Tarefas (Bearer token obrigatório)

| Método | Rota | Descrição |
| --- | --- | --- |
| `POST` | `/tasks` | Cria tarefa |
| `GET` | `/tasks` | Lista tarefas do usuário |
| `GET` | `/tasks/{id}` | Busca tarefa |
| `PUT` | `/tasks/{id}` | Atualiza tarefa |
| `DELETE` | `/tasks/{id}` | Remove tarefa |

## Estrutura

```text
server/
├── main.py           # Rotas e aplicação FastAPI
├── security.py       # bcrypt + JWT
├── database/         # Engine, sessão e init_db
├── entities/         # Modelos SQLAlchemy (User, Task)
├── schemas/          # Schemas Pydantic
├── usecases/         # Regras de negócio
└── tests/            # Testes pytest
```

## Testes

```bash
pytest tests/ -v
```

Os testes usam SQLite em memória e recriam o schema a cada teste.

> ⚠️ Os testes de tarefas esperam `{"tasks": [...]}`, mas a API retorna um array. Atualize os testes ou o contrato da API.

## Observações

- O banco SQLite é criado em `database/db.sqlite` no startup.
- O frontend fica em [`../client`](../client) e usa `http://127.0.0.1:8000`.
- Consulte o [README principal](../README.md) para limitações conhecidas (campo `completed`, `PATCH`, CORS etc.).
