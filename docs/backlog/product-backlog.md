# Product Backlog — Multiplagier

Documento da **Oficina de Product Backlog** (ISG-022, FATEC Mauá, 2026/2).  
Fonte didática: [docs/references/isg022/ISG022_Oficina_Product_Backlog.pdf](../references/isg022/ISG022_Oficina_Product_Backlog.pdf).  
Riscos de referência: [docs/security/initial-risks.md](../security/initial-risks.md).

Issues no GitHub: https://github.com/vitor-dandrea/multiplagier/issues

---

## Product Goal

Permitir que consumidores comprem eletrônicos e acessórios online no Multiplagier, e que administradores gerenciem o catálogo e os pedidos, com autenticação, autorização por perfil/recurso e rastreabilidade básica de ações sensíveis.

---

## Atores / personas

| Ator | Objetivos legítimos | Dados / ações | Restrições |
|------|---------------------|---------------|------------|
| Visitante | Navegar no catálogo público | Ver produtos ativos | Não acessa pedidos, perfil nem painel admin |
| Cliente | Comprar, acompanhar pedidos e gerenciar conta | Próprios dados cadastrais, carrinho e pedidos | Não acessa dados de outros clientes nem área admin |
| Administrador | Operar a loja (catálogo, pedidos, usuários) | CRUD de produtos, status de pedidos, visão operacional | Ações críticas auditáveis; menor privilégio além do papel |

---

## Épicos (capacidades)

| ID | Épico | Descrição |
|----|-------|-----------|
| E-01 | Identidade e Acesso | Cadastro, autenticação, sessão e papéis (Cliente / Admin) |
| E-02 | Catálogo | Listagem, detalhe e gestão de produtos eletrônicos |
| E-03 | Carrinho e Pedidos | Montar carrinho, criar e consultar pedidos |
| E-04 | Administração | Painel para operação do catálogo e dos pedidos |
| E-05 | Auditoria e Segurança | Controles transversais (AuthZ, rate limit, XSS, logs) |

---

## Itens do backlog

Legenda: **Must / Should / Could / Won't now** (MoSCoW). Estimativa: **P / M / G**.

| ID | Épico | Item | Tipo | Prioridade | Estim. | Status | Issue |
|----|-------|------|------|------------|--------|--------|-------|
| US-001 | E-01 | Cadastrar cliente | Story | Must | M | Backlog | #13 |
| US-002 | E-01 | Autenticar e encerrar sessão (login/logout) | Story | Must | M | Backlog | #13 |
| US-003 | E-02 | Listar catálogo público de produtos | Story | Must | M | Ready | #16 |
| US-004 | E-02 | Visualizar detalhe de um produto | Story | Must | P | Ready | #17 |
| US-005 | E-02 / E-04 | Admin cria e edita produtos | Story | Must | G | Backlog | #12 |
| US-006 | E-03 | Cliente cria pedido a partir do carrinho | Story | Must | G | Backlog | #18 |
| US-007 | E-03 | Cliente consulta **seus** pedidos | Story | Must | M | Backlog | #19 |
| US-008 | E-04 | Admin altera status do pedido | Story | Should | M | Backlog | #20 |
| US-009 | E-02 | Buscar / filtrar produtos por categoria | Story | Could | M | Backlog | #1 |
| SEC-001 | E-05 | Autorizar acesso por recurso em pedidos (anti-IDOR) | Security | Must | M | Ready | #15 |
| SEC-002 | E-05 | Restringir área admin por papel no servidor | Security | Must | M | Ready | #21 |
| SEC-003 | E-05 | Limitar tentativas de login (rate limit / lockout) | Security | Must | M | Backlog | #13 |
| SEC-004 | E-05 | Validar entradas e escapar saídas (anti-XSS) | Security | Should | M | Backlog | #7 |
| DOC-001 | — | Product Backlog e Sprint Goal versionados | Task | Must | P | Done | — |
| DOC-002 | — | Inventário formal de ativos e requisitos de segurança | Task | Should | M | Backlog | #14 |
| DOC-003 | — | ADR monólito DDD-lite + atualizar arquitetura | Task | Could | M | Won't now | #6 |
| TECH-001 | — | Esqueleto Domain/Application/Infrastructure (Catalog) | Task | Could | G | Won't now | #1 |
| TECH-002 | — | Dockerizar Laravel 8 + PostgreSQL | Task | Could | G | Won't now | #11 |

Total de itens iniciais: **18** (dentro da faixa 12–20 da oficina).

---

## Critérios de aceite (itens prioritários)

### US-001 — Cadastrar cliente

Como **visitante**, quero criar uma conta de cliente para poder autenticar e comprar.

- Formulário exige nome, e-mail e senha com validação server-side.
- E-mail duplicado é rejeitado com mensagem clara.
- Senha é armazenada com hash (bcrypt / hash do Laravel).
- Após cadastro bem-sucedido, o usuário pode autenticar (US-002).
- Nenhum dado sensível é logado em claro.

### US-002 — Autenticar e encerrar sessão

Como **cliente cadastrado**, quero autenticar minha identidade para acessar funcionalidades protegidas.

- Credenciais válidas abrem sessão autenticada.
- Credenciais inválidas são negadas com mensagem genérica (sem revelar se o e-mail existe, quando aplicável).
- Logout encerra a sessão.
- Rotas protegidas rejeitam visitante não autenticado.
- Relacionado a **SEC-003** (rate limit).

### US-003 — Listar catálogo público

Como **visitante**, quero ver a lista de produtos ativos para escolher o que comprar.

- Apenas produtos `is_active` aparecem.
- Cada item exibe ao menos nome e preço.
- Catálogo vazio mostra estado compreensível.
- Página pública não exige autenticação.
- Erros de backend não expõem stack trace ao cliente.

### US-004 — Detalhe do produto

Como **visitante**, quero ver o detalhe de um produto para decidir a compra.

- Produto ativo exibe nome, descrição, preço e disponibilidade.
- Produto inexistente ou inativo retorna 404 (ou equivalente seguro).
- Saídas escapadas no template (base de **SEC-004**).

### US-005 — Admin cria e edita produtos

Como **administrador**, quero criar e editar produtos para manter o catálogo atualizado.

- Apenas usuário com papel Admin conclui a operação (**SEC-002**).
- Cliente/visitante recebe acesso negado no servidor.
- Campos obrigatórios validados (nome, preço, etc.).
- Alterações refletem na listagem pública quando o produto está ativo.

### US-006 — Cliente cria pedido

Como **cliente autenticado**, quero finalizar um pedido a partir do carrinho para comprar os itens.

- Pedido fica associado ao usuário autenticado.
- Visitante não consegue criar pedido.
- Itens e totais do pedido são persistidos de forma coerente.
- Depende de autenticação (US-002) e catálogo (US-003/004).

### US-007 — Cliente consulta seus pedidos

Como **cliente**, quero consultar meus pedidos para acompanhar o andamento.

- Usuário autenticado visualiza somente os próprios pedidos.
- A lista informa identificador, data, status e valor.
- Usuário sem pedidos recebe estado vazio compreensível.
- Requisição não autenticada é negada.
- Erro de backend não expõe stack trace ou dados internos.
- **Segurança:** alinhado a **SEC-001** / risco **R-01**.

#### Given / When / Then (US-007 + SEC-001)

```text
Dado que o usuário A está autenticado
E existe um pedido pertencente ao usuário B
Quando A solicitar o pedido de B
Então a aplicação deve negar o acesso
E não deve retornar dados do pedido
E a tentativa negada pode ser registrada sem conteúdo sensível
```

### US-008 — Admin altera status do pedido

Como **administrador**, quero alterar o status de um pedido para refletir a operação da loja.

- Somente Admin altera status.
- Transições inválidas são rejeitadas.
- Cliente não altera status de pedidos (próprios ou de terceiros).

### SEC-001 — Autorização por recurso (pedidos)

**Contexto:** Risco **R-01** — acesso horizontal a dados de pedidos.  
**Requisito SEC-01:** a aplicação deve validar autorização por recurso no backend.

- Usuário A não acessa recurso de B.
- A decisão ocorre no servidor (não só no frontend).
- Tentativas negadas são registradas sem conteúdo sensível.
- Testes automatizados cobrem acesso permitido e negado (quando a feature existir).

### SEC-002 — Papel Admin no servidor

**Contexto:** Risco **R-03** — escalada para painel administrativo.

- Rotas `/admin` (ou equivalente) exigem autenticação + papel Admin.
- Cliente autenticado recebe negação ao chamar rotas admin diretamente.
- Novas rotas admin nascem protegidas por padrão (secure by default).

### SEC-003 — Rate limit no login

**Contexto:** Risco **R-02** — abuso do fluxo de autenticação.

- Após N tentativas falhas em janela de tempo, novas tentativas são limitadas ou bloqueadas temporariamente.
- Mensagens de erro não facilitam enumeração indevida de contas.
- Eventos de falha relevantes podem ser auditados sem senha em claro.

### SEC-004 — Validação e escape (XSS)

**Contexto:** Risco **R-04**.

- Inputs de nome/descrição validados no servidor.
- Templates usam escape padrão (ex.: Blade `{{ }}`).
- Não renderizar HTML não confiável sem política explícita.

---

## Sprint Goal (próxima Sprint)

Deixar o Product Backlog Ready e selecionar o incremento mínimo de **catálogo (US-003, US-004)** e preparação de **identidade (US-001, US-002)**, com **SEC-001, SEC-002 e SEC-003** definidos como requisitos rastreáveis ligados a R-01, R-03 e R-02.

### Candidatos Ready / Must para a próxima Sprint

| ID | Justificativa |
|----|---------------|
| DOC-001 | Backlog versionado (este documento) — Done nesta entrega |
| US-003 | Incremento demonstrável de catálogo |
| US-004 | Completa o fluxo de navegação pública |
| SEC-001 | Risco alto; critérios já definidos para AuthZ |
| SEC-002 | Necessário antes do CRUD admin (US-005) |
| US-001 / US-002 | Base para pedidos e área autenticada |

### Won't now (explícito)

- DOC-003 / TECH-001 — monólito DDD-lite e esqueleto Catalog (implementação futura)
- TECH-002 — Docker
- US-009 — busca/filtro avançado

---

## Definition of Ready (DoR)

Um item só entra na Sprint se:

- Objetivo e contexto estão claros
- Critérios de aceite definidos
- Dependências conhecidas
- Impacto de segurança identificado quando aplicável
- Tamanho compatível (P/M) ou item decomposto
- Dúvidas críticas resolvidas ou registradas como hipótese

## Definition of Done (DoD)

- Implementação/documentação integrada e executável quando aplicável
- Critérios de aceite atendidos
- Review por outro integrante quando relevante
- Testes relevantes executados
- Nenhum segredo real versionado
- Docs / baseline atualizados se a mudança impactar segurança
- Issue/PR com evidência suficiente

---

## Checklist da oficina

- [x] Product Goal explica resultado/valor
- [x] Atores principais identificados
- [x] Épicos organizam o produto (não são tarefas)
- [x] Itens prioritários com comportamento verificável
- [x] Critérios de aceite objetivos (incl. Given/When/Then em SEC-001)
- [x] Segurança explícita (≥3 itens SEC)
- [x] Itens grandes quebrados / Won't now explícito
- [x] Prioridade com valor/risco
- [x] Dependências críticas visíveis nas histórias
- [x] Próxima Sprint com Goal e candidatos Ready
- [x] Backlog versionado e rastreável (este arquivo + GitHub Issues)

---

## Histórico

| Data | Evento |
|------|--------|
| 2026-09-02 | Product Backlog inicial da oficina; alinhamento às Issues existentes |
