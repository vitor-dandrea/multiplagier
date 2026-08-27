# Initial Security Risks — Multiplagier

Cenários iniciais de risco (ISG022). Escala de probabilidade/impacto é qualitativa, para priorizar discussão — não é medição científica.

Legenda: **B** = Baixo, **M** = Médio, **A** = Alto.

| ID | Ativo | Ameaça | Vulnerabilidade | Impacto (negócio) | Prob. | Impacto | Risco | Controles / decisões | Residual |
|----|-------|--------|-----------------|-------------------|-------|---------|-------|----------------------|----------|
| R-01 | Pedidos / dados do cliente | Usuário autenticado malicioso | Endpoint consulta pedido por ID sem verificar se o pedido pertence ao usuário (IDOR) | Exposição de dados de outros clientes; perda de confiança | M | A | **Alto** | AuthZ por recurso no backend; testes negativos; log de negação; resposta sem dados extras | Bug de regra/config ainda possível — exige testes e code review |
| R-02 | Sessão / credenciais | Atacante externo (força bruta / credential stuffing) | Login sem limitação de tentativas e sem monitoramento | Sequestro de conta, pedidos fraudulentos | M | A | **Alto** | Rate limit / lockout progressivo; senhas com hash (bcrypt Laravel); logs de falha sem vazar se usuário existe | Credenciais vazadas de outros serviços ainda funcionam sem MFA |
| R-03 | Painel administrativo | Atacante externo ou usuário comum | Rotas admin protegidas só no frontend ou sem checagem de papel no servidor | Alteração de preços/catálogo; vazamento em massa | M | A | **Alto** | Middleware/policy de papel Admin; secure by default; fail secure | Erro humano ao registrar nova rota admin sem middleware |
| R-04 | Catálogo / páginas com conteúdo gerado | Atacante injeta script (XSS armazenado/refletido) | Saída não escapada em Blade / inputs de nome/descrição | Roubo de sessão, defacement, phishing na loja | M | M | **Médio** | Escape padrão Blade `{{ }}`; sanitização; CSP futura | Conteúdo rico (HTML) se introduzido sem política clara |
| R-05 | Credenciais de ambiente / DB | Exposição acidental no Git ou máquina compartilhada | `.env` ou dump com senha commitado / compartilhado | Comprometimento total do ambiente e dados | B | A | **Médio** | `.gitignore`; `.env.example` sem segredos; revisão de PR; rotação se vazar | Notebooks pessoais e backups locais ainda precisam de disciplina |

## Cenários detalhados (mínimo 3)

### R-01 — IDOR em pedidos

- **Ativo:** Pedidos e dados associados ao cliente  
- **CIA/AUT:** Confidencialidade + Integridade  
- **Ameaça:** Usuário malicioso autenticado  
- **Vulnerabilidade:** Consulta por ID sem ownership check  
- **Exploração:** Alterar `order_id` na URL/API  
- **Impacto:** Acesso a dados de outros clientes  
- **Princípios:** Secure by default; menor privilégio; fronteira de confiança  
- **Decisão:** Backend autoriza cada recurso; frontend nunca decide acesso  
- **Controles:** Policy/ACL + testes permitido/negado + log de negação  

### R-02 — Abuso do fluxo de autenticação

- **Ativo:** Credenciais e sessão  
- **CIA/AUT:** Confidencialidade + Autenticidade  
- **Ameaça:** Atacante externo automatizado  
- **Vulnerabilidade:** Ausência de throttling no login  
- **Exploração:** Tentativas massivas de senha  
- **Impacto:** Conta comprometida  
- **Princípios:** Defesa em profundidade; fail secure  
- **Decisão:** Adotar rate limiting e hashing forte desde a Sprint de identidade  
- **Controles:** `RateLimiter` / lockout; bcrypt; mensagens genéricas de erro  

### R-03 — Escalada para administração

- **Ativo:** Painel administrativo  
- **CIA/AUT:** Autenticidade + Integridade + Confidencialidade  
- **Ameaça:** Usuário comum ou atacante com conta cliente  
- **Vulnerabilidade:** Falta de autorização server-side por papel  
- **Exploração:** Chamar rotas `/admin/*` diretamente  
- **Impacto:** Manipulação do catálogo e da operação  
- **Princípios:** Menor privilégio; secure by default  
- **Decisão:** Papel Admin explícito + middleware obrigatório  
- **Controles:** Gate/Policy; testes de papel; inventário de rotas admin  

## Prioridade imediata para o backlog

1. R-01 e R-03 — modelar AuthZ cedo (evitar “segurança no final”).  
2. R-02 — junto da implementação de login (Sprint 3).  
3. R-04 / R-05 — higiene contínua (PRs, Blade, `.env`).
