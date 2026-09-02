# Multiplagier

E-commerce acadêmico desenvolvido para a disciplina **ISG022 — Segurança no Desenvolvimento de Aplicações** (FATEC Mauá, 2026/2).

O produto nasce simples e evolui com **Security by Design**: a cada Sprint da disciplina, novos requisitos, ameaças, controles e evidências entram no backlog e no repositório.

## Status

**Oficina de Product Backlog** — backlog inicial versionado; implementação de features nas próximas Sprints.

## Product Goal

Permitir que consumidores comprem eletrônicos e acessórios online no Multiplagier, e que administradores gerenciem o catálogo e os pedidos, com autenticação, autorização por perfil/recurso e rastreabilidade básica de ações sensíveis.

Documento completo: [docs/backlog/product-backlog.md](docs/backlog/product-backlog.md) · Issues: https://github.com/vitor-dandrea/multiplagier/issues

## Visão do produto

Plataforma de venda de eletrônicos e acessórios (TVs, tablets, áudio, informática etc.) com:

- catálogo de produtos;
- conta de cliente;
- carrinho e pedidos;
- painel administrativo.

## Usuários principais

| Perfil | Descrição |
|--------|-----------|
| Visitante | Navega no catálogo sem autenticação |
| Cliente | Conta autenticada; gerencia perfil, carrinho e pedidos próprios |
| Administrador | Gerencia catálogo, pedidos e usuários (permissões elevadas) |

Os perfis **Cliente** e **Administrador** atendem ao requisito mínimo de dois contextos de autorização distintos.

## Equipe (ciclo inicial)

| Papel | Integrante |
|-------|------------|
| Tech Lead | Vitor |
| Product Manager | Marco |
| Team Member | Mauro |


## Stack

- PHP 8.0
- Laravel 8
- PostgreSQL
- Laravel Herd (ambiente local do Tech Lead)
- Docker (planejado para as próximas Sprints, para rodar em qualquer máquina)

## Como executar (Herd + PostgreSQL)

### Pré-requisitos

- [Laravel Herd](https://herd.laravel.com/) com PHP 8.0 ativo
- PostgreSQL local em execução
- Composer (vem com o Herd)
- Node/npm apenas se for compilar assets front-end

### Passos

Repositório: https://github.com/vitor-dandrea/multiplagier

```bash
git clone https://github.com/vitor-dandrea/multiplagier.git
cd multiplagier
composer install
copy .env.example .env
php artisan key:generate
```

Ajuste no `.env`:

```env
APP_NAME=Multiplagier
APP_URL=http://multiplagier.test
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=multiplagier
DB_USERNAME=postgres
DB_PASSWORD=sua_senha_local
```

Crie o banco e rode as migrations:

```bash
# no psql / pgAdmin: CREATE DATABASE multiplagier;
php artisan migrate
```

Vincule o site no Herd (na pasta do projeto):

```bash
herd link multiplagier
```

Acesse: [http://multiplagier.test](http://multiplagier.test)

> Alternativa sem Herd: `php artisan serve` e abra `http://127.0.0.1:8000`.

### Segredos

- **Nunca** versione `.env`, senhas, tokens ou chaves reais.
- Use `.env.example` apenas com valores de exemplo.

## Estrutura relevante

```text
multiplagier/
├── README.md
├── docs/
│   ├── backlog/
│   │   └── product-backlog.md
│   ├── architecture/
│   │   └── architecture-v1.md
│   ├── security/
│   │   ├── security-baseline.md
│   │   └── initial-risks.md
│   └── references/
│       └── isg022/          # PDFs da disciplina
├── app/
├── routes/
└── ...
```

## Acordo de trabalho do grupo

- Product Backlog em **GitHub Issues** e em [docs/backlog/product-backlog.md](docs/backlog/product-backlog.md).
- Desenvolvimento em **branches de curta duração** a partir de `main` (ex.: `feat/...`, `docs/...`, `fix/...`).
- Evitar commits diretos permanentes em `main` para mudanças relevantes.
- Integração via **Pull Request**; pelo menos **um outro integrante** revisa quando possível.
- Commits pequenos, descritivos e rastreáveis à Issue.
- Não versionar segredos; revisar `.gitignore` antes do push.
- Definition of Done alinhada à disciplina: código revisado, critérios de aceite, docs atualizadas e impacto de segurança avaliado quando aplicável.

## Documentação

### Produto e backlog

- [Product Backlog](docs/backlog/product-backlog.md)

### Segurança e arquitetura (Sprint 0 / baseline)

- [Architecture v1](docs/architecture/architecture-v1.md)
- [Security Baseline](docs/security/security-baseline.md)
- [Initial Risks](docs/security/initial-risks.md)

### Referências da disciplina (PDFs)

- [docs/references/isg022/](docs/references/isg022/) — Projeto Integrador, Security Baseline e Oficina de Product Backlog

## Roadmap resumido (ISG022)

| Sprint | Foco |
|--------|------|
| 0 | Fundação (repositório, baseline) |
| — | Oficina Product Backlog (este marco) |
| 1 | Fundamentos e riscos / início de implementação |
| 2 | Arquitetura segura / threat model |
| 3 | Identidade e acesso |
| 4 | Mitigações OWASP |
| 5 | Observabilidade e testes |
| 6 | Hardening e defesa |
| Final | Produto + demonstração |

## Licença

Projeto acadêmico — uso educacional. Os PDFs em `docs/references/isg022/` são material didático da FATEC/ISG-022; ver atribuição naquela pasta.
