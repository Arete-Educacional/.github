# Arete — Tech Stack & Funcionalidades

## Funcionalidades Iniciais

### Gestão Familiar
- Registro de família
    - Cadastro de filhos (nome, idade, nível)
    - Cadastro de responsáveis
        - Acesso compartilhado entre responsáveis

### Registro de Atividades
- **Ensino Clássico** (Trivium)
    - Gramática, Lógica, Retórica
    - Registro de aulas
    - Registro de leituras
    - Outros tipos de atividades (artes, esportes, museus, etc.)
- Acompanhamento de virtudes
- Suporte familiar


### Relatórios
- Por filho
- Por família
- Período
- Disciplina
- Outros

---

## Funcionalidades Futuras

- Marketplace de ferramentas e serviços
- Sugestão de livros
- Ferramentas de apoio à família
- Integração com IA (sugestões de plano de estudo)
- App multilíngue

---

## Tech Stack

### Backend
| Tecnologia | Papel |
|------------|-------|
| Go | Linguagem principal da API |
| Gin | Framework HTTP |
| GORM | ORM |
| SQLite | Banco de dados (mudança futura para PostgreSQL) |
| JWT | Autenticação |

### Frontend
| Tecnologia | Papel |
|------------|-------|
| Flutter | Framework cross-platform |
| Web | Painel web para responsáveis |
| Android / iOS | App mobile |
| i18n | Suporte multilíngue desde o início (pt-br apenas, para o MVP) |

**Painéis planejados:**
- Family Panel
- Activities Panel
- Reports Panel
- Futuro
    - Marketing Panel
---
- Admin
    - Admin Panel
    - Coupon Management Panel
    - Marketplace Panel (futuro)

### DevOps
| Tecnologia | Papel |
|------------|-------|
| Docker | Containerização |
| Dokku | Deploy VPS (mudança futura para Cloud Provider) |
| GitHub Actions | CI/CD |
| Cloudflare | DNS / proxy / segurança |

---

## Decisões de Produto

### Usuários e Acesso
O usuário principal é o **responsável** — ele gerencia a família, registra atividades e acompanha relatórios. Alunos não têm acesso ao app no MVP. Múltiplos responsáveis podem compartilhar acesso à mesma família.

### Modelo de Negócio
O app é **SaaS multi-família** — uma única instância serve todas as famílias com isolamento de dados por tenant.

### Acompanhamento de Virtudes

Uma virtude é definida por semana (ex: Perseverança). No final de cada dia, o responsável responde perguntas simples sobre o filho:

- Hoje ele terminou o que começou?
- Continuou mesmo quando estava difícil?
- Desistiu rápido de algo?

**Escala de resposta:** Sim / Mais ou menos / Não (ou escala visual 🙂 😐 🙁)

O app gera um **relatório semanal** automático:
> *"Nesta semana, João demonstrou perseverança em 4 de 7 dias."*

**Vantagens do modelo:** simples, rápido, fácil de implementar e engaja a família no acompanhamento diário.

**Estrutura semanal no app:**
- Virtude da semana (do currículo anual de 52 virtudes)
- Definição simples (ex: *"Continuar fazendo o que é certo, mesmo quando é difícil."*)
- Pergunta diária de reflexão (ex: *"Hoje eu desisti fácil ou continuei tentando?"*)
- Espaço para registro da família

**Relatório anual de formação do caráter:**
No final do ano o app gera automaticamente um mapa de evolução da criança, agrupado por eixos:
- Autodisciplina
- Relação com os outros
- Vida intelectual
- Liderança e serviço

### Plano de Estudos
No MVP, cada família monta seu próprio plano. No futuro, haverá planos pré-definidos baseados em idade e nível, com opção de personalização e sugestões via IA (atividades, leituras, baseadas no perfil da família).

### Relatórios
O usuário escolhe entre relatórios automáticos (semanal/mensal) ou geração manual. Os relatórios são filtráveis por filho, período e disciplina. MVP: visualização in-app apenas. Exportação (PDF, CSV) é funcionalidade futura.

### Banco de Dados
SQLite no MVP. Migração planejada para PostgreSQL conforme crescimento da base de usuários e necessidades de escalabilidade. Isolamento multi-tenant via **row-level isolation** — todas as tabelas terão `family_id` como chave de escopo.

### Autenticação
MVP: e-mail e senha com **verificação de e-mail** e fluxo de recuperação de senha. Futuro: autenticação social (Google, Apple).

### Flutter Web & Mobile
Codebase única para web e mobile. Adaptações de UI serão feitas pontualmente conforme necessidade, mantendo uma base unificada para facilitar manutenção. MVP: distribuído como **PWA instalável**, sem publicação nas lojas. Futuro: publicação na App Store e Play Store com conta própria da Arete.

### Infraestrutura
Deploy via Dokku em VPS. Provedor a definir (Hetzner, DigitalOcean, etc.). Migração futura para cloud provider conforme escala.

### Suporte Familiar

O suporte é acionado pelo responsável via botão no app Flutter. A solicitação é registrada no banco de dados e encaminhada como issue no GitHub, preservando a privacidade dos dados da família.

**Fluxo:**
```
[Botão "Suporte" no Flutter]
        ↓
[Formulário: nome, email, mensagem]
        ↓
[API Go salva no DB + cria issue no GitHub + envia email de confirmação]
        ↓
[Família recebe: "Sua solicitação foi recebida"]
        ↓
[Time responde no GitHub → webhook → email para a família]
```

**Repositório GitHub:**
- Repo público e exclusivo para suporte: `arete-educacional/suporte`
- Issues abertas ao público, código permanece em repo privado separado

**Registro no banco de dados:**
- Nome real do solicitante
- E-mail do solicitante
- Mensagem
- Data/hora do envio
- Referência ao número da issue criada no GitHub

**Issue no GitHub:**
- Nome do solicitante **anonimizado** (ex: `Família #42`) — dados pessoais ficam apenas no DB
- Mensagem completa
- Data/hora

**Resposta automática por e-mail:**
- Confirmação imediata ao solicitante: *"Sua solicitação foi recebida."*
- Respostas do time chegam também por e-mail via webhook do GitHub

### Suporte Offline
Sem suporte offline no MVP. No futuro: sincronização local com SQLite no device.

### MVP
Escopo mínimo para validação com as primeiras famílias:
- Registro de família e filhos
- Registro de atividades (ensino clássico — Trivium)
- Relatórios básicos
- Suporte familiar (comunicação entre responsáveis e equipe Arete)

---

## Perguntas em Aberto

### Produto
1. O **suporte familiar** é um canal de comunicação com a equipe Arete (chat/ticket) ou funcionalidades de apoio dentro do app (ex: dicas, guias)?
### Negócio
8. Qual o **modelo de cobrança** — assinatura mensal, anual, freemium? O painel de cupons sugere descontos — haverá período de trial gratuito?
9. Qual **gateway de pagamento** será usado (Stripe, Mercado Pago, etc.)?
    - MVP: possivelmente, nenhum — foco em validação. Futuro: integração com gateway para assinaturas.
10. O **Admin Panel** gerenciará apenas usuários e assinaturas, ou também conteúdo (planos de estudo, livros sugeridos)?
    - Tudo, conforme o roadmap de funcionalidades futuras.
11. O **Marketplace** será aberto a terceiros (professores, editoras) ou apenas produtos/serviços da Arete?
    - Futuro: aberto a terceiros, com curadoria da Arete para garantir qualidade e alinhamento com a filosofia do ensino clássico.

