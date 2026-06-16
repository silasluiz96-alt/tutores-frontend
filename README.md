# Tutores — Frontend

Painel administrativo e widget de chat para a **Plataforma de Tutores Personalizados**, desenvolvida como desafio técnico da DOT Digital Group.

> Código produzido com auxílio de agentes de codificação (Claude Code — Anthropic).

---

## O que faz

- **Painel Admin** (`/admin`) — cadastra tutores, lista os existentes e gera o código de embed
- **Widget de Chat** (`/widget`) — janela de conversa que roda dentro de um `<iframe>` em qualquer site

Não há instalação. HTML, CSS e JavaScript puro — roda direto no navegador.

---

## Como rodar localmente

```bash
# Opção 1 — npx serve (recomendado)
npx serve . --listen 5500

# Opção 2 — Python
python -m http.server 5500
```

Acesse:
- Painel Admin: `http://localhost:5500/admin/`
- Widget (teste direto): `http://localhost:5500/widget/?tutor_id=1&tutor_nome=Meu+Tutor&backend_url=http://localhost:8000`

> O backend deve estar rodando em `http://localhost:8000`.
> Consulte o [README do backend](https://github.com/silasluiz96-alt/tutores-backend) para instruções.

---

## Fluxo de embed — ponta a ponta

```
Administrador                Plataforma                   Site do cliente
     │                            │                             │
     ├── Preenche formulário ───▶ │                             │
     │   (título, instruções,     │                             │
     │    fontes do tutor)        │                             │
     │                            ├── POST /api/v1/tutores/ ──▶ │
     │◀── Tutor criado (id: 42) ──┤                             │
     │                            │                             │
     ├── Clica "Gerar Embed" ───▶ │                             │
     │◀── Snippet <iframe> ───────┤                             │
     │                            │                             │
     ├── Cola snippet no site ─────────────────────────────────▶│
     │                            │                             │
     │                            │        Usuário final abre o site
     │                            │                             │
     │                            │◀── POST /api/v1/chat/42 ───┤
     │                            ├── Resposta do tutor ───────▶│
```

**O snippet gerado tem este formato:**

```html
<iframe
  src="http://localhost:5500/widget/index.html?tutor_id=42&tutor_nome=Nome%20do%20Tutor"
  width="400"
  height="600"
  frameborder="0"
  title="Chat — Nome do Tutor"
  allow="clipboard-write">
</iframe>
```

---

## Parâmetros do widget (URL)

| Parâmetro | Obrigatório | Descrição |
|---|---|---|
| `tutor_id` | Sim | ID do tutor no banco de dados |
| `tutor_nome` | Não | Nome exibido no cabeçalho do chat |
| `sessao_id` | Não | Identificador da sessão (gerado automaticamente se omitido) |
| `backend_url` | Não | URL do backend (padrão: `http://localhost:8000`) |

---

## Funcionalidades do painel admin

- Criar tutores com título, descrição e instruções de comportamento
- Campos de fonte: Lattes, LinkedIn e campo primário para site/portfólio
- Botão "+" para adicionar fontes extras ilimitadas (GitHub, DOI, YouTube etc.)
- API Key solicitada uma vez via `prompt()` e salva no `localStorage`
- Lista de tutores com badge de status (ativo/inativo) e botão de desativação
- Gerador de snippet de embed com botão de cópia

---

## Estrutura de arquivos

```
admin/
└── index.html     ← painel do administrador

widget/
└── index.html     ← chat embeddável via iframe

public/
└── style.css      ← tema cyber dark (compartilhado entre as páginas)
```

---

## Tema visual

Tema **Cyber Dark** definido em variáveis CSS:

```css
--bg:        #050d1a   /* fundo principal */
--bg-card:   #0a1628   /* cartões */
--neon:      #00d4ff   /* ciano neon — cor primária */
--neon2:     #7b2fff   /* roxo — mensagens do usuário */
--danger:    #ff2d6b   /* vermelho — ações destrutivas */
```

---

## Qualidade e linter

O frontend é composto por HTML, CSS e JavaScript puro — sem etapa de build, sem bundler e sem dependências de pacotes. Por isso, não há linter de build configurado (ferramentas como ESLint exigem Node.js e npm, o que contraria a premissa do projeto de rodar direto no navegador). A qualidade é garantida pelos testes do backend e pela revisão manual do código via Pull Request.

---

## Limitações do MVP

- A API Key do admin é salva em `localStorage` — sem criptografia local
- O widget não renderiza Markdown nas respostas (texto simples)
- Sem histórico visual de sessões anteriores para o usuário final
- O snippet gerado aponta para `localhost` — em produção é necessário substituir pela URL real

---

## Próximos passos para produção

- Hospedar o frontend em CDN (ex: Vercel, Netlify, Cloudflare Pages)
- Substituir `localStorage` da API Key por autenticação JWT com sessão segura
- Suporte a Markdown nas respostas do tutor (ex: `marked.js`)
- Streaming de respostas para feedback visual imediato
- Customização visual do widget por tutor (cores, logo, posição)
- Aviso obrigatório de IA na interface do chat (conformidade LGPD)

---

*Desafio técnico DOT Digital Group — Plataforma de Tutores Personalizados*
*Código produzido com auxílio de agentes de codificação (Claude Code — Anthropic)*
