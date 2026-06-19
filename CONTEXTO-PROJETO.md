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
| og-image.png | Imagem de prévia para links (WhatsApp/redes) | /paulofranca/og-image.png |

---

## ARQUITETURA DO SISTEMA

### Fluxo completo
```
Cliente preenche anamnese.html
    → grava localStorage (pf_perfil_cliente)
    → baixa automaticamente: anamnese_NomeCliente_data.json

Cliente preenche pesos.html
    → lê anamnese do localStorage
    → mescla os dois documentos
    → grava localStorage (pf_perfil_cliente) — agora completo
    → baixa automaticamente: perfil_completo_NomeCliente_data.json

Paulo abre motor.html
    → detecta perfil no localStorage automaticamente
    → ou importa perfil_completo_*.json manualmente
    → seleciona imóveis do banco
    → gera relatório de compatibilidade
```

### localStorage
- Chave: `pf_perfil_cliente` — perfil JSON do cliente atual
- Chave: `pf_perfil_etapa` — 'anamnese' ou 'completo'
- Chave: `pf_empreendimentos_v1` — banco de imóveis de Paulo
- **Importante:** localStorage é local ao navegador. Paulo deve sempre usar o mesmo navegador e computador para o Motor e o banco de empreendimentos.

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

---

## PRÓXIMAS ETAPAS PLANEJADAS

### Prioridade alta
- **Filtros excludentes no Motor** — critérios que eliminam imóveis antes da comparação (faixa de valor, bairros excluídos, finalidade, financiamento). Configuráveis por Paulo — ele define quais são inegociáveis.

### Prioridade média
- **Banco de Clientes** — histórico de perfis com busca por nome, reabertura no Motor sem precisar importar JSON
- **Template de e-mail do Pesos** — configurar template_pesos_v1 no EmailJS para envio funcionar
- **Verificar logo residual** — checar se a imagem "Paulo França" ainda aparece em algum ponto específico identificado pelo usuário (pendente de revisão, não alterar sem confirmação)

### Prioridade baixa / futuro
- **Renomear repositório** — de `paulofranca` para `curadoria-imobiliaria` (tentativa anterior não foi salva — lembrar de clicar no botão "Renomeação", não só editar o campo)
- **Domínio próprio** — paulofranca.com.br apontando para o GitHub Pages
- **Banco de dados online** — Supabase ou Firebase para persistir perfis de clientes entre dispositivos, eliminando dependência do localStorage
- **Proteção legal formal** — considerar consulta a advogado especializado em propriedade intelectual para registro de software (INPI) e/ou marca, caso o uso comercial justifique

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

*Documento atualizado em: Junho 2026*
*Sistema desenvolvido com Claude (Anthropic)*
