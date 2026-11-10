# Plugin `galpee-admin` — GALPEE no Claude

Conecta o Claude (claude.ai, Claude Desktop, Cowork e Claude Code) ao
backoffice GALPEE por MCP. **Somente leitura**; toda chamada fica no log de
auditoria da plataforma. **Só contas de Administrador** conseguem autorizar.

Fonte do plugin: `plugins/galpee-admin` no repositório `stallae/galpee`
(privado). Publicado em `stallae/galpee-plugin` (público) para instalação.

## 1. Como a autenticação funciona

Ao conectar, o Claude **abre a página de login do GALPEE** (`/entrar`). O
Administrador entra com e-mail, senha e o código do 2FA; a página devolve o
Claude autorizado. Não há token para copiar.

Por trás: OAuth 2.1 com registro dinâmico de cliente e PKCE, servido pelo
próprio GALPEE (`/.well-known/oauth-authorization-server`,
`/api/auth/mcp/*`). O token de acesso vale 1 h e é renovado sozinho por até
30 dias. Conta que deixar de ser ADMIN, for bloqueada ou suspensa perde o
acesso na hora. Desligar a flag **Acesso via Claude (MCP)** em
Funcionalidades corta o acesso de todo mundo.

## 2. Instalar

### claude.ai e Claude Desktop (chat)

1. **Personalizar → Plugins → Adicionar → Adicionar marketplace**.
2. URL: `github.com/stallae/galpee-plugin` (repositório público).
3. Marque **galpee-admin** e adicione.
4. Abra o plugin, aba **Conectores**, e clique **Conectar** em *galpee*: a
   página de login do GALPEE abre; entre como Administrador.

Alternativa sem plugin: **Personalizar → Conectores → Adicionar conector
personalizado**, URL `https://galpee.com.br/api/mcp`, autenticação
**Entrar agora** (*Sign in now*).

### Claude Code (terminal e aba Code do Desktop)

```bash
claude plugin marketplace add stallae/galpee-plugin
claude plugin install galpee-admin@galpee
```

Na primeira chamada, o Claude Code abre o navegador na página de login.
Para reautenticar: `/mcp` → *galpee* → *Authenticate*.

Sem plugin, direto ao servidor:

```bash
claude mcp add --transport http galpee https://galpee.com.br/api/mcp
```

### Outro ambiente (QA, local)

O plugin aponta para a produção, `https://galpee.com.br` (`galpee.stallae.com` redireciona para lá). Para outro ambiente, use o
conector personalizado ou `claude mcp add` com a URL desejada (por exemplo
`https://qa.galpee.com.br/api/mcp` ou `http://localhost:3000/api/mcp`).

### Token pessoal (sem OAuth)

Para clientes que não fazem OAuth, o Admin pode emitir um token `glp_mcp_…`
em **Segurança → Acesso via Claude (MCP)** e enviá-lo como
`Authorization: Bearer glp_mcp_…` — por exemplo, no Claude Code:

```bash
claude mcp add --transport http galpee https://galpee.com.br/api/mcp \
  --header "Authorization: Bearer glp_mcp_…"
```

O token aparece uma única vez, vale 30/90/365 dias e pode ser revogado na
mesma tela.

## 3. Usar

Pergunte em português:

- "Como está a plataforma hoje?" → `resumo_plataforma`
- "Quantas visitas o site teve essa semana? De onde vêm? Quantos no celular?" → `metricas_site` (`dias: 7`)
- "O que aconteceu com o protocolo GLP-20261019-0007?" → `detalhar_solicitacao`
- "Quais galpões de SP estão esperando revisão?" → `listar_galpoes`
- "Quem aprovou galpões esta semana?" → `consultar_auditoria` com `WAREHOUSE_APPROVED`

O skill `galpee-admin` (carregado com o plugin) ensina o vocabulário canônico
e as regras de conduta (não promete ações, PII com parcimônia).

## 4. Verificar

```bash
claude plugin validate plugins/galpee-admin
claude mcp list          # "galpee" deve aparecer conectado
```

Erro `401` = sessão OAuth expirada ou conta sem perfil ADMIN (reconecte);
`503` = flag `mcp_admin` desligada.
