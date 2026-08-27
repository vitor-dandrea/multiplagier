# Security Baseline — Multiplagier

Baseline inicial de segurança (ISG022 — Guia Prático Security Baseline).
Objetivo desta versão: identificar o que proteger, riscos plausíveis e decisões que orientam a evolução. **Não** implementar todos os controles nesta Sprint.

## 1. Contexto do sistema

**Multiplagier** é um e-commerce acadêmico inspirado na Multilaser. Usuários navegam em um catálogo de eletrônicos, criam conta, montam carrinho e realizam pedidos. Administradores gerenciam catálogo e operações.

**Usuários envolvidos:** Visitante, Cliente autenticado, Administrador.

**Stack atual:** PHP 8.0, Laravel 8, PostgreSQL, Herd (local). Docker planejado.

## 2. Ativos prioritários

| Ativo | Valor | CIA/AUT | Responsável / componente |
|-------|-------|---------|---------------------------|
| Credenciais e sessão de usuários | Comprometimento permite impersonação e abuso de conta | C + AUT | Laravel Auth / sessão |
| Dados cadastrais de clientes | Exposição afeta privacidade e confiança | C + I | Backend / PostgreSQL |
| Pedidos e histórico de compra | Contém dados pessoais e financeiros de fluxo de compra | C + I | Backend / PostgreSQL |
| Catálogo e preços | Alteração indevida causa prejuízo e fraude | I + A | Admin / Backend |
| Painel e credenciais administrativas | Acesso privilegiado a toda a operação | C + AUT | Admin / AuthZ |
| Logs de auditoria | Necessários para investigar incidentes | I + A | Operação / storage de logs |

## 3. Princípios adotados

### Menor privilégio

- Cliente só acessa próprios pedidos e perfil.
- Admin é papel explícito; visitante não possui operações privilegiadas.
- Conta de banco da aplicação terá permissões mínimas necessárias (evolução; hoje ambiente local de desenvolvimento).

### Defesa em profundidade

- AuthN + AuthZ server-side + validação de entrada + CSRF + logging.
- Se o frontend falhar em esconder uma ação, o backend ainda nega.

### Redução da superfície de ataque

- Evitar expor endpoints administrativos sem autenticação.
- Não expor debug/stack traces em produção (`APP_DEBUG=false`).
- Não versionar `.env` nem segredos.

### Secure by default

- Novas rotas sensíveis nascem protegidas por middleware de autenticação/autorização.
- Negar por padrão quando o papel ou a posse do recurso for ambígua.

### Fail secure

- Falha de autenticação, autorização ou dependência crítica → acesso negado / erro genérico ao cliente.
- Não “abrir” o fluxo em caso de erro interno.

### Fronteiras de confiança

- Browser e payloads HTTP são não confiáveis.
- Decisão de autorização ocorre apenas no Laravel (servidor).
- PostgreSQL acessível somente pela aplicação.

## 4. Riscos residuais e premissas

- Laravel 8 está em fim de vida; existem avisos de segurança conhecidos — risco aceito temporariamente por restrição de stack do grupo; mitigação futura via atualização ou hardening/WAF quando viável.
- Ambiente local compartilhado via Herd ainda não está dockerizado: risco de divergência de configuração entre máquinas.
- Controles de AuthZ por recurso, rate limiting e auditoria ainda não implementados (backlog).
- Dados de demonstração serão fictícios; sem PII real.

## 5. Próximos passos

1. Formalizar inventário de ativos e matriz de riscos (Sprint 1).
2. Threat model e revisão do diagrama (Sprint 2).
3. Implementar autenticação + papéis Cliente/Admin (Sprint 3).
4. Mitigações OWASP alinhadas aos riscos R-01..R-0n (Sprint 4).
5. Dockerizar ambiente para o time.
