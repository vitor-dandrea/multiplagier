# Multiplagier

E-commerce acadêmico inspirado na [Multilaser](https://www.multilaser.com.br/), desenvolvido para a disciplina **ISG022 — Segurança no Desenvolvimento de Aplicações** (FATEC Mauá, 2026/2).

O produto nasce simples e evolui com **Security by Design**: a cada Sprint da disciplina, novos requisitos, ameaças, controles e evidências entram no backlog e no repositório.

## Status

**Sprint 0 — Fundação**

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

> **Nota:** o documento oficial da disciplina sugere equipes de 5 integrantes. Este grupo inicia com 3 pessoas; alinhar com o professor se necessário.

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

```bash
git clone <url-do-repositorio> multiplagier
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
│   ├── architecture/
│   │   └── architecture-v1.md
│   └── security/
│       ├── security-baseline.md
│       └── initial-risks.md
├── app/
├── routes/
└── ...
```

## Acordo de trabalho do grupo

- Product Backlog em **GitHub Issues** (e Project, se adotado).
- Desenvolvimento em **branches de curta duração** a partir de `main` (ex.: `feat/...`, `docs/...`, `fix/...`).
- Evitar commits diretos permanentes em `main` para mudanças relevantes.
- Integração via **Pull Request**; pelo menos **um outro integrante** revisa quando possível.
- Commits pequenos, descritivos e rastreáveis à Issue.
- Não versionar segredos; revisar `.gitignore` antes do push.
- Definition of Done alinhada à disciplina: código revisado, critérios de aceite, docs atualizadas e impacto de segurança avaliado quando aplicável.

## Documentação de segurança (Sprint 0 / baseline)

- [Architecture v1](docs/architecture/architecture-v1.md)
- [Security Baseline](docs/security/security-baseline.md)
- [Initial Risks](docs/security/initial-risks.md)

## Roadmap resumido (ISG022)

| Sprint | Foco |
|--------|------|
| 0 | Fundação (este repositório) |
| 1 | Fundamentos e riscos |
| 2 | Arquitetura segura / threat model |
| 3 | Identidade e acesso |
| 4 | Mitigações OWASP |
| 5 | Observabilidade e testes |
| 6 | Hardening e defesa |
| Final | Produto + demonstração |

## Licença

Projeto acadêmico — uso educacional.
