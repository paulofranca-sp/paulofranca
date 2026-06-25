# CONTEXTO DO PROJETO — Sistema de Curadoria Imobiliária
## Paulo França · Curadoria Imobiliária de Alto Padrão

---

## SISTEMA ONLINE

**URL principal:**
https://paulofranca-sp.github.io/paulofranca/

**GitHub:**
https://github.com/paulofranca-sp/paulofranca

**E-mail do sistema (EmailJS):**
paulofranca.spimoveis@gmail.com
Service ID: service_alesa01
Template Anamnese → Paulo: template_nro7ud3
Template Anamnese → Cliente: template_p504cqh
Template Pesos: template_pesos_v1 (a configurar no EmailJS)

**Link de agendamento:**
https://calendar.app.google/dffTZGue9T52RuEe9

---

## ARQUIVOS DO SISTEMA

| Arquivo | Função | URL |
|---|---|---|
| index.html | Página de entrada | /paulofranca/ |
| anamnese.html | Diagnóstico do cliente — 5 Pilares | /paulofranca/anamnese.html |
| pesos.html | Mapa de Preferências — 27 critérios | /paulofranca/pesos.html |
| empreendimentos.html | Banco de imóveis | /paulofranca/empreendimentos.html |
| motor.html | Motor de Compatibilidade | /paulofranca/motor.html |
| precos.html | Painel de atualização rápida de preços (uso interno, celular) | /paulofranca/precos.html |
| og-image.png | Imagem de prévia para links (WhatsApp/redes) | /paulofranca/og-image.png |

---

## ARQUITETURA DO SISTEMA

### Fluxo completo (ATUALIZADO — Junho 2026, com Supabase)
```
Cliente preenche anamnese.html
    → grava localStorage (pf_perfil_cliente) — cache local da sessão
    → grava também na nuvem (Supabase, tabela perfis_clientes, etapa='anamnese')
    → envia e-mail de notificação (texto resumido, via EmailJS)

Cliente preenche pesos.html
    → lê anamnese do localStorage (mesma sessão/navegador do cliente)
    → mescla os dois documentos
    → grava localStorage (pf_perfil_cliente) — agora completo
    → grava também na nuvem (Supabase, mesma tabela, etapa='completo', mesmo e-mail = upsert)
    → envia e-mail de notificação (texto resumido, via EmailJS)

Paulo abre motor.html (em QUALQUER navegador/computador, não precisa mais ser o mesmo do teste)
    → busca automaticamente na nuvem (Supabase) a lista de perfis com etapa='completo'
    → mostra em "Clientes Online" — Paulo clica no nome do cliente desejado
    → (ainda existe, como alternativa: detecção automática via localStorage se for mesmo navegador, e importação manual de JSON)
    → busca também na nuvem o catálogo de empreendimentos (tabela empreendimentos)
    → seleciona imóveis do banco
    → gera relatório de compatibilidade
```

### BANCO DE DADOS ONLINE — SUPABASE (implementado Junho 2026)

Motivo da mudança: o `localStorage` é isolado por navegador/dispositivo. Um cliente
preenchendo o formulário no celular nunca poderia "chegar" automaticamente no
computador de Paulo — são aparelhos diferentes. A solução foi migrar a troca de
dados client↔Paulo para um banco online (Supabase), mantendo o localStorage apenas
como cache de sessão (mesmo navegador).

**Projeto Supabase:**
- Project ref: `akvnoguimatxfbagbdgo`
- Project URL: `https://akvnoguimatxfbagbdgo.supabase.co`
- Região: South America (São Paulo)
- Chave pública usada no código (`sb_publishable_...`): segura para ficar embutida nos arquivos `.html`, pois as permissões são limitadas pelas políticas RLS abaixo.
- **Chave secreta (`sb_secret_...`): NUNCA deve entrar em nenhum arquivo `.html` público.** Fica guardada só por Paulo, fora do projeto.

**Tabela `perfis_clientes`** (perfis de anamnese/pesos):
```sql
create table perfis_clientes (
  id uuid primary key default gen_random_uuid(),
  email text unique not null,
  nome text,
  telefone text,
  etapa text,            -- 'anamnese' ou 'completo'
  dados jsonb,           -- perfil completo (mesmo formato do localStorage)
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);
-- RLS habilitado, políticas permitem insert/update/select para 'anon'
```
Upsert por `email` (on_conflict=email) — quando o cliente completa o Mapa de
Preferências, o mesmo registro é atualizado (não duplica).

**Tabela `empreendimentos`** (catálogo de imóveis):
```sql
create table empreendimentos (
  id text primary key,   -- mesmo id já usado no localStorage (Date.now().toString())
  nome text,
  estagio text,          -- 'Na planta' | 'Em obras' | 'Pronto novo' | 'Revenda'
  valor text,            -- valor formatado, ex: "2.500.000"
  updated_at timestamptz default now(),
  dados jsonb            -- item completo (mesmo formato do localStorage)
);
-- RLS habilitado, políticas permitem insert/update/select/delete para 'anon'
```

**Onde cada arquivo usa o Supabase:**
- `anamnese.html` e `pesos.html` — ao enviar, gravam (upsert) em `perfis_clientes`.
- `motor.html` — ao abrir, busca `perfis_clientes` (etapa='completo') para a lista "Clientes Online", e busca `empreendimentos` para o catálogo de imóveis (substituindo a antiga leitura exclusiva do localStorage).
- `empreendimentos.html` — toda criação/edição/exclusão de imóvel grava também no Supabase. Tem um botão **"Sincronizar"** no topo, usado uma única vez para migrar o catálogo que já existia no localStorage para a nuvem.
- `precos.html` (NOVO) — painel leve, mobile-first, para Paulo atualizar preços sem abrir o Banco de Empreendimentos completo. Duas funções:
  1. **Edição manual** de preço por imóvel (busca por nome).
  2. **Reajuste percentual por estágio** — aplica um % sobre o preço atual de todos os imóveis de um estágio (Na planta / Em obras / Pronto novo / Revenda) de uma vez. O cálculo sempre usa o preço atual como base (não há preço-base fixo), então ajustes manuais feitos antes nunca são "perdidos" — o próximo reajuste percentual parte do que estiver salvo no momento.

### localStorage (ainda usado como cache local/sessão)
- Chave: `pf_perfil_cliente` — perfil JSON do cliente atual (sessão do navegador)
- Chave: `pf_perfil_etapa` — 'anamnese' ou 'completo'
- Chave: `pf_empreendimentos_v1` — cópia local do catálogo (sincronizada com Supabase)
- **Isso já não é mais uma limitação crítica:** com o Supabase, Paulo pode abrir o Motor, o Banco de Empreendimentos e o Painel de Preços em qualquer navegador/dispositivo — os dados vêm da nuvem. O localStorage permanece só como cache de carregamento rápido.

---

## OS 27 CRITÉRIOS (IDs espelho entre pesos.html e empreendimentos.html)

| ID | Categoria | Critério |
|---|---|---|
| sc_loc1 | Localização | Perfil e reputação do bairro |
| sc_loc2 | Localização | Proximidade a trabalho, escola ou família |
| sc_loc3 | Localização | Mobilidade urbana e acesso viário |
| sc_loc4 | Localização | Comércio, gastronomia e serviços a pé |
| sc_imo1 | Imóvel | Metragem e número de suítes |
| sc_imo2 | Imóvel | Funcionalidade da planta |
| sc_imo3 | Imóvel | Luminosidade e posição solar |
| sc_imo4 | Imóvel | Vista e andar |
| sc_seg1 | Segurança | Padrão de segurança do condomínio |
| sc_seg2 | Segurança | Infraestrutura técnica do prédio |
| sc_seg3 | Segurança | Chegada e privacidade na entrada |
| sc_laz1 | Lazer | Espaços fitness e bem-estar |
| sc_laz2 | Lazer | Espaços de convivência e coworking |
| sc_laz3 | Lazer | Estrutura pet-friendly |
| sc_laz4 | Lazer | Áreas verdes e contato com a natureza |
| sc_fin1 | Financeiro | Aderência ao orçamento |
| sc_fin2 | Financeiro | Potencial de valorização |
| sc_fin3 | Financeiro | Liquidez de revenda |
| sc_fin4 | Financeiro | Geração de renda por locação |
| sc_per1 | Perfil | Afinidade com o DNA da construtora |
| sc_per2 | Perfil | Padrão construtivo e acabamentos |
| sc_per3 | Perfil | Arquitetura e assinatura do projeto |
| sc_per4 | Perfil | Sustentabilidade, eficiência e tecnologia |
| sc_srv1 | Serviços | Wellness |
| sc_srv2 | Serviços | Longevity Living |
| sc_srv3 | Serviços | Branded Residences |
| sc_srv4 | Serviços | Hospitality Services |

---

## FÓRMULA DE COMPATIBILIDADE

```
sinergia(critério) = (score_imóvel / 10) × (peso_cliente / 5)

score_global = Σ(sinergia × peso_cliente) / Σ(peso_cliente) × 100
```

- Score imóvel: 1–10 (avaliado por Paulo no cadastro)
- Peso cliente: 1–5 (definido pelo cliente no Mapa de Preferências)
- Resultado: 0–100 por critério, por categoria e global

---

## ANAMNESE — ESTRUTURA DOS 5 PILARES

| Pilar | Campo JSON | Conteúdo |
|---|---|---|
| I — Motivação | p1_* | motivacao, sentimento, sensacoes, mudanca, valores, planos, importancia |
| II — Patrimonial | p2_* | finalidade, faixa, margem, outros_imoveis, tipo, objetivos, retorno, financiamento, risco |
| III — Funcional | p3_* | rotina, moradores, conviventes, estagio, itens, restricoes, reforma, bairros, evitar |
| IV — Experiência | p4_* | experiencia, desafios, decisao, ideal, avaliacao, disponib |
| V — Legado | p5_* | horizonte, legado, valores_lp, futureproof, importancia, visao |

---

## VERSÕES DOS ARQUIVOS

| Arquivo online | Versão local equivalente |
|---|---|
| anamnese.html | anamnese_v3_7_2.html |
| pesos.html | pesos_v2.html |
| empreendimentos.html | empreendimentos_v1.html |
| motor.html | motor_v1.html |

---

## NOMENCLATURA PADRONIZADA (voltada ao cliente)

| Etapa | Nome de apresentação |
|---|---|
| Etapa 1 | **Anamnese Imobiliária** |
| Etapa 2 | **Mapa de Preferências** |
| Tela final etapa 2 | "Mapa de Preferências concluído!" |
| Botão de agendamento | **"Falar com Paulo França"** |

Importante: todo texto visível ao cliente usa esses nomes. Nomes técnicos de arquivo (pesos_v2, anamnese_v3_7_2 etc.) nunca aparecem na interface.

---

## LINKS ABSOLUTOS (não relativos)

Toda navegação entre páginas usa URL completa
(`https://paulofranca-sp.github.io/paulofranca/pesos.html`)
em vez de caminho relativo (`pesos.html`).
Isso foi necessário porque links relativos falhavam
quando abertos via WhatsApp em alguns navegadores móveis
(erro "não foi possível acessar o arquivo").

---

## OPEN GRAPH (prévia de link no WhatsApp/redes sociais)

Todas as páginas têm meta tags Open Graph e Twitter Card no `<head>`,
apontando para `og-image.png` (fundo azul-marinho #0C1B2E,
texto "PAULO FRANÇA" em branco e "CURADORIA IMOBILIÁRIA" em dourado).

Isso faz o link mostrar uma prévia visual elegante quando compartilhado,
sem alterar nada do funcionamento ou layout das páginas em si.

Atenção: apps de mensagem cacheiam a prévia — pode levar algumas horas
para atualizar após mudança na imagem ou nas meta tags.

---

## O QUE ESTÁ FUNCIONANDO

- ✓ Sistema publicado online no GitHub Pages
- ✓ Link clicável funcionando em computador e celular (URLs absolutas)
- ✓ Anamnese completa com 5 Pilares e envio de e-mail
- ✓ Mapa de Preferências com 27 critérios calibráveis
- ✓ Nomenclatura unificada e amigável ao cliente (sem termos técnicos)
- ✓ Telas de conclusão limpas — sem botões de download visíveis ao cliente
- ✓ Mesclagem automática Anamnese + Pesos via localStorage (silenciosa)
- ✓ Botão único de agendamento: "Falar com Paulo França"
- ✓ Banco de Empreendimentos com dados objetivos + 27 scores espelho
- ✓ Dashboard, filtros, modal de detalhe, export/import JSON (uso interno)
- ✓ Motor de Compatibilidade com ranking e relatório por categoria
- ✓ Relatório com sinergias, pontos fortes, pontos de atenção e narrativa
- ✓ Impressão em PDF do relatório
- ✓ Logo "Paulo França" removida da Anamnese (substituída por texto)
- ✓ Texto de abertura da Anamnese reformulado (tom consultivo)
- ✓ Meta tags Open Graph — prévia elegante ao compartilhar links
- ✓ Aviso de copyright no rodapé (© 2025 Paulo França)
- ✓ Banco de dados online (Supabase) — perfis de clientes e catálogo de imóveis persistidos na nuvem, acessíveis de qualquer navegador/dispositivo
- ✓ Motor busca automaticamente clientes recebidos ("Clientes Online") e catálogo de imóveis direto da nuvem
- ✓ Banco de Empreendimentos sincroniza automaticamente com a nuvem a cada cadastro/edição/exclusão (+ botão "Sincronizar" para migração inicial)
- ✓ Painel de Preços (precos.html) — atualização manual de preço por imóvel + reajuste percentual em massa por estágio, acessível do celular

---

## PRÓXIMAS ETAPAS PLANEJADAS

### Prioridade alta
- **Filtros excludentes no Motor** — critérios que eliminam imóveis antes da comparação (faixa de valor, bairros excluídos, finalidade, financiamento). Configuráveis por Paulo — ele define quais são inegociáveis.

### Prioridade média
- **Banco de Clientes (tela dedicada)** — PENDENTE, levantado em Junho/2026: hoje o Motor já lista os clientes recebidos via nuvem na seção "Clientes Online", mas de forma simples (sem busca/filtro/histórico organizado). Construir uma tela própria, no estilo do Banco de Empreendimentos, com busca por nome, data de resposta e reabertura direta no Motor. Não é urgente — é melhoria de conforto, mais relevante quando o volume de clientes crescer. Lembrar de perguntar a Paulo se já é hora de priorizar isso.
- **Template de e-mail do Pesos** — configurar template_pesos_v1 no EmailJS para envio funcionar
- **Verificar logo residual** — checar se a imagem "Paulo França" ainda aparece em algum ponto específico identificado pelo usuário (pendente de revisão, não alterar sem confirmação)
- **Campo "unidades disponíveis"** — discutido em Junho/2026 junto com o Painel de Preços, mas adiado por Paulo. Adicionar quando ele pedir, seguindo o mesmo padrão do campo de preço (manual + sincronizado com Supabase).

### Prioridade baixa / futuro
- **Renomear repositório** — de `paulofranca` para `curadoria-imobiliaria` (tentativa anterior não foi salva — lembrar de clicar no botão "Renomeação", não só editar o campo)
- **Domínio próprio** — paulofranca.com.br apontando para o GitHub Pages
- **Proteção legal formal** — considerar consulta a advogado especializado em propriedade intelectual para registro de software (INPI) e/ou marca, caso o uso comercial justifique

### Concluído em Junho/2026 (não repetir)
- ~~Banco de dados online (Supabase/Firebase)~~ — feito com Supabase. Ver seção "BANCO DE DADOS ONLINE — SUPABASE" acima para detalhes de tabelas, chaves e onde cada arquivo usa.

---

## DECISÕES DE UX IMPORTANTES (não reverter sem necessidade)

1. **Nenhum arquivo é baixado automaticamente para o cliente** — isso foi removido deliberadamente. Gerava ruído técnico ("JSON", "Baixar perfil") incompatível com a sofisticação esperada por um público de alto padrão sensível a confiança financeira/comportamental.
2. **Um único botão de ação por tela de conclusão** — nunca múltiplos botões técnicos concorrendo com a ação principal (agendar ou avançar etapa).
3. **Nomenclatura do cliente ≠ nomenclatura técnica** — arquivos podem ter nomes de versão internos, mas a interface sempre usa os nomes padronizados da tabela acima.

---

## COMO ATUALIZAR OS ARQUIVOS NO GITHUB

1. Edite o arquivo localmente
2. Acesse github.com/paulofranca-sp/paulofranca
3. Clique no arquivo a substituir
4. Clique no ícone de lápis (editar) ou faça novo upload
5. Clique em "Commit changes"
6. O site atualiza em 1–2 minutos

---

## MANTER BACKUP LOCAL

Mantenha uma pasta no computador com todos os arquivos do site,
sempre sincronizada com o que está no GitHub:

```
curadoria-imobiliaria/
├── anamnese.html
├── pesos.html
├── index.html
├── motor.html
├── empreendimentos.html
├── precos.html
├── og-image.png
└── CONTEXTO-PROJETO.md
```

---

## COMO USAR ESTE DOCUMENTO

Cole o conteúdo deste arquivo no início de qualquer nova conversa
com o Claude antes de pedir alterações ou evoluções no sistema.
Isso garante que o assistente entenda toda a arquitetura construída
e continue de onde paramos sem retrabalho.

---

*Documento atualizado em: Junho 2026 (inclui migração para Supabase, Painel de Preços e correção do fluxo Anamnese→Pesos→Motor)*
*Sistema desenvolvido com Claude (Anthropic)*
