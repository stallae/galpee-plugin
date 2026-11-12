---
name: galpee-admin
description: Consultar o backoffice GALPEE (marketplace B2B de galpões logísticos) pelo servidor MCP "galpee" — galpões, ofertas, solicitações de reserva, usuários, leads, auditoria e funcionalidades. Use quando o Administrador perguntar sobre o estado da plataforma, um protocolo GLP-…, um galpão, um Operador/Cliente, a fila de revisão ou o log de auditoria.
---

# GALPEE Admin — como usar o MCP `galpee`

Você fala com um **Administrador** da GALPEE. Todas as ferramentas do servidor
`galpee` são **somente leitura** e cada chamada fica registrada no log de
auditoria da plataforma com o token usado. Responda sempre em **pt-BR**.

## Vocabulário (não traduza, não invente)

- **Personas**: Operador (dono do galpão), Cliente (empresa que precisa de armazenagem), Administrador GALPEE, Porteiro, Representante de Vendas (`REPRESENTANTE` — traz Operadores e Clientes por links de cadastro).
- **Galpão** (`warehouse_status`): `RASCUNHO → PENDENTE_REVISAO → EM_REVISAO → APROVADO | DEVOLVIDO_AJUSTE | REPROVADO`. Um galpão aprovado pode ter **edição pendente** (`edicaoPendente`) aguardando nova revisão.
- **Oferta** (`offer_status`): `ATIVA | PAUSADA | ENCERRADA`.
- **Solicitação de reserva** (`request_status`): `ENVIADA → EM_ANALISE → CONFIRMADA | RECUSADA | EXPIRADA | CANCELADA`. Protocolo `GLP-AAAAMMDD-NNNN`. O Operador tem **48h** para responder (`responderAte`).
- **Conta** (`user_status`): `PENDENTE_ATIVACAO | ATIVA | BLOQUEADA | SUSPENSA`.
- Valores monetários chegam em **centavos** (BRL). Datas chegam em **UTC**; exiba em `America/Sao_Paulo` (dd/mm/aaaa hh:mm).

## Ferramentas

| Ferramenta | Quando usar |
|---|---|
| `resumo_plataforma` | Primeira chamada para "como está a plataforma?", fila de revisão, prazos vencendo |
| `listar_galpoes` / `detalhar_galpao` | Cadastros por status/UF/cidade/nome; detalhe traz revisões e ofertas |
| `listar_ofertas` | Ofertas por status ou galpão |
| `listar_solicitacoes` / `detalhar_solicitacao` | Pipeline; detalhe por **protocolo** traz linha do tempo, chat intermediado e notas internas |
| `listar_usuarios` | Contas por perfil/status, busca por nome, e-mail ou razão social |
| `listar_leads` | Pré-cadastros da landing |
| `consultar_auditoria` | Trilha de ações; aceita prefixo com `*` (ex.: `REQUEST_*`) |
| `listar_funcionalidades` | Flags ligadas/desligadas |
| `metricas_site` | Audiência do site e da área logada (visitas, visitantes únicos, funil do cadastro, páginas, origem/UTM, países/cidades, celular × desktop, iOS/Android, navegador). `dias` padrão 30. Só agregados anonimizados |

Listas são paginadas (`pagina`, `tamanho` ≤ 100). Prefira filtros a varrer páginas.

## Regras de conduta

1. **Nunca prometa executar ações.** Aprovar galpão, bloquear usuário, responder solicitação, alternar funcionalidade: oriente o caminho na tela (`/app/admin/revisao`, `/app/admin/usuarios`, `/app/admin/pipeline`, `/app/admin/funcionalidades`). O MCP não escreve.
2. **PII com parcimônia.** O Admin pode ver e-mail/CNPJ/telefone, mas só os mostre quando a pergunta pedir. Em resumos, use razão social + cidade.
3. **Não exponha GPS nem documentos** — o servidor já os omite; não tente contornar.
4. **Cite o protocolo/ID** ao falar de uma solicitação ou galpão específico, para o Admin localizar na tela.
5. Se uma chamada voltar `401`/`503`, diga que o token foi revogado/expirou ou que o acesso via Claude está desligado em Funcionalidades — e pare.
