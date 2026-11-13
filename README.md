# API de tarefas — Curso de DevOps da PUC

Aplicação de gerenciamento de tarefas desenvolvida com FastAPI. A API permite criar, listar, consultar, concluir e remover tarefas armazenadas em PostgreSQL. O repositório também contém uma aplicação simples para notificar a conclusão de tarefas.

## Tecnologias

- Python 3.14
- FastAPI e Uvicorn
- SQLModel e SQLAlchemy
- PostgreSQL, com o driver Psycopg
- Pytest e HTTPX para testes
- Docker
- Nginx (configuração de proxy em `default.conf`)
- GitHub Actions (testes, lint, publicação da imagem Docker e deploy)

## Execução local

### Pré-requisitos

- Python 3.14
- Um servidor PostgreSQL acessível a partir da máquina

### Instalação

Na raiz do repositório, crie e ative um ambiente virtual e instale as dependências:

```powershell
py -3.14 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

Configure as variáveis de ambiente usadas pela API para conectar ao PostgreSQL:

```powershell
$env:DATABASE_USERNAME = "seu_usuario"
$env:DATABASE_PASSWORD = "sua_senha"
$env:DATABASE_ADDRESS = "localhost:5432"
$env:DATABASE = "nome_do_banco"
```

No Bash (Linux, macOS ou Codespaces), configure as mesmas variáveis no terminal em que iniciará a API:

```bash
export DATABASE_USERNAME="seu_usuario"
export DATABASE_PASSWORD="sua_senha"
export DATABASE_ADDRESS="localhost:5432"
export DATABASE="nome_do_banco"
```

O banco informado precisa existir. Ao iniciar, a aplicação cria as tabelas do modelo se elas ainda não existirem.

### Iniciar a API

Na raiz do projeto e com o ambiente virtual ativado:

```powershell
python -m uvicorn app.api:APP --reload
```

A API ficará disponível em `http://127.0.0.1:8000`. A documentação interativa do FastAPI está em `http://127.0.0.1:8000/docs`.

Rotas principais:

- `GET /` — mensagem inicial
- `GET /autor` — autor da aplicação
- `GET /tarefas` — lista as tarefas
- `POST /tarefas?titulo=...&descricao=...` — cria uma tarefa (`descricao` é opcional)
- `GET /tarefas/{id_tarefa}` — consulta uma tarefa
- `PUT /tarefas/{id_tarefa}` — conclui uma tarefa
- `DELETE /tarefas/{id_tarefa}` — remove uma tarefa

### Aplicação de notificações (opcional)

A aplicação independente de notificações pode ser iniciada em outro terminal:

```powershell
python -m uvicorn app.notificacao:NOTIFICACAO --port 8002 --reload
```

Ela expõe `POST /notificar`, recebendo os parâmetros `_id` e `titulo`.

### Testes

Os testes da API exigem que as quatro variáveis de ambiente de banco estejam definidas para importar a aplicação. O cliente de teste usa SQLite para os dados dos testes:

```powershell
$env:DATABASE_USERNAME = "mock"
$env:DATABASE_PASSWORD = "mock"
$env:DATABASE_ADDRESS = "localhost"
$env:DATABASE = "mock"
python -m pytest
```

## Docker e automação

O `Dockerfile` descreve a imagem da API. O workflow em `.github/workflows/python-app.yml` instala as dependências, executa o Pylint e os testes, publica a imagem Docker e aciona o deploy configurado no GitHub Actions.
