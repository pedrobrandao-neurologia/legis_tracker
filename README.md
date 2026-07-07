# LegisTracker

Dashboard de monitoramento de proposições legislativas da Câmara dos Deputados do Brasil, com análise estratégica baseada no Regimento Interno (RICD).

![React](https://img.shields.io/badge/React-18-blue)
![API](https://img.shields.io/badge/API-Dados%20Abertos%20C%C3%A2mara-green)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

## Visão Geral

O LegisTracker permite acompanhar em tempo real a tramitação de projetos de lei na Câmara dos Deputados, oferecendo:

- Atualização automática dos andamentos de cada proposição monitorada
- Rotina de leitura em três janelas diárias (08h, 14h e 18h) com marcação de "visto" por proposição
- Detecção de proposições pautadas em eventos legislativos (reuniões de comissão e sessões de Plenário)
- Identificação do relator designado e contagem do prazo de emendas em comissão (Art. 119, I, RICD)
- Visão dedicada de relatorias do parlamentar de interesse
- Marcação de prioridade (com motivação) e de votação (com data), persistidas localmente
- Análise estratégica com recomendações baseadas no Regimento Interno para acelerar a tramitação
- Interpretação contextualizada de documentos acessórios (emendas, requerimentos, recursos, destaques)
- Notificações de mudanças de regime, apreciação e novas tramitações

## Funcionalidades

### Dashboard

- **Painel de resumo** com contadores de não vistos na janela de leitura atual, itens em pauta (próximos 7 dias), prioritários, projetos em urgência, plenário, comissão e total monitorado
- **Abas Monitorados | Relatorias** — visão geral de tudo que é monitorado e formato dedicado para os projetos relatados pelo parlamentar de interesse
- **Cards de projeto** exibindo para cada proposição:
  - **Título como link** — abre a ficha de tramitação oficial no site da Câmara em nova aba (`fichadetramitacao?idProposicao={id}`)
  - **Último andamento** (texto do `despacho`/`descricaoTramitacao` da tramitação mais recente) com data/hora e órgão — substitui o antigo campo "Situação"
  - **Relator designado** (nome, partido, órgão e data de designação, extraídos das tramitações) ou "Relator não designado"
  - **Prazo de emendas em comissão** — 5 sessões a partir da designação do relator (Art. 119, I, RICD), com estimativa em dias úteis
  - **Nota de pauta** quando a proposição está pautada em evento legislativo (data/hora, órgão e objeto da apreciação)
  - Badges de estado: `Não visto`, `Prioritário`, `Votado dd/mm/aaaa`, `Em pauta hoje/amanhã/dd-mm`
  - Motivo da priorização (quando informado)
  - Regime de tramitação (Urgência, Prioridade, Ordinário) e tipo de apreciação (Plenário, Comissão, Poder Conclusivo)
  - Rito processual que o projeto seguirá
  - Recomendação estratégica principal para acelerar a tramitação, com base legal no RICD
- **Filtros e ordenação** por tipo, ano da proposição, ano da votação, regime, apreciação, comissão, área temática, status de leitura (vistas/não vistas), prioridade e votação; ordenação por andamento mais recente, prioritários primeiro, urgência, tipo/número ou ano
- **Modo escuro/claro**

### Rotina de Leitura (08h • 14h • 18h)

Rotina de leitura em três janelas diárias, pensada para varreduras de manhã, início e fim da tarde:

- Cada proposição pode ser **marcada como "vista"** individualmente (botão no card ou nos detalhes) ou em lote ("Marcar todos como vistos")
- Itens **ainda não vistos** ganham destaque visual (borda e badge âmbar) e são contados no painel de resumo ("Não vistos — janela atual")
- **Regra de reset**: o "visto" vale para o conteúdo lido — se surgir **novo andamento** após a marcação, o item volta automaticamente a "não visto" na janela seguinte (ou na atual, ao atualizar)
- O timestamp do último "visto" e a tramitação lida são persistidos em localStorage por proposição

### Notas de Pauta (eventos legislativos)

- Consulta `GET /eventos` (hoje até +7 dias) e `GET /eventos/{id}/pauta` para descobrir quando uma proposição monitorada está **pautada em reunião de comissão ou sessão de Plenário**
- Cards e detalhes exibem a **nota de pauta**: data/hora do evento, órgão, tipo de evento e o objeto/fase da apreciação prevista (com regime e relator do item, quando informados)
- Badge de destaque: `Em pauta hoje`, `Em pauta amanhã` ou `Em pauta dd/mm`
- A verificação roda ao carregar o app, quando a lista de monitorados muda e a cada atualização manual/polling

### Relatorias

- **Aba "Relatorias"** com layout dedicado para proposições em que o parlamentar de interesse é o **relator**
- Um projeto entra na visão de relatorias quando: (a) foi adicionado pela busca por deputado na aba "Relator"; ou (b) o relator extraído das tramitações corresponde ao **parlamentar de interesse** configurado em Configurações
- Cada card de relatoria mostra: relator, órgão, **data de designação**, **prazo do parecer** (Art. 52 RICD — 10 sessões em regime ordinário, 5 em prioridade/urgência), **prazo de emendas** (Art. 119, I, RICD) e a **situação da relatoria** (aguardando parecer / parecer já apresentado)

### Priorização e Votação

- **Prioritário** — toggle por proposição (estrela no card ou checkbox nos detalhes) acompanhado de **campo de texto livre para a motivação**; prioritários ganham destaque visual, filtro e ordenação próprios
- **Votado + data** — marcação manual nos detalhes (checkbox + campo de data) e **detecção automática** a partir de tramitações que indiquem votação/aprovação (ex.: "Aprovado o projeto", "Aprovada a redação final", "Transformado em lei"); a detecção nunca sobrescreve uma marcação manual
- **Filtro por ano** em duas dimensões, claramente separadas: **ano da proposição** (ano de apresentação) e **ano da votação** (ano registrado no campo "votado")

### Busca e Monitoramento

- **Busca por proposição** — formato `PL 1234/2023`, `PEC 45/2019`, etc. **Todos os tipos de proposição são aceitos**: PL, PEC, PLP, PDL, PDC, PRC, MPV, PLV, PLN, MSC, REQ, RIC, entre outros (qualquer sigla válida da API)
- **Busca por deputado** — pesquisa pelo nome e exibe todas as proposições como autor/coautor ou relator; itens adicionados pela aba "Relator" são marcados como relatorias do deputado
- **Acervo completo carregado automaticamente** — ao selecionar o deputado, o app busca sozinho todas as páginas da API (que pagina de 100 em 100), em lotes paralelos e com indicador de progresso; não é preciso clicar em "carregar mais"
- **Monitorar Todos** — botão para adicionar de uma vez todas as proposições de um deputado ao monitoramento (centenas de projetos); a adição é imediata e os detalhes de regime/apreciação são completados em segundo plano

### Detalhes do Projeto

O cabeçalho dos detalhes traz o **título com link para a ficha oficial da Câmara**, o **último andamento** (no lugar do antigo campo "Situação"), as **notas de pauta** de eventos legislativos, o bloco de **Relatoria** (relator designado, data de designação e prazo de emendas conforme o Art. 119, I, RICD) e o painel de **Gestão do Monitoramento** (visto, prioritário + motivação, votado + data).

Além disso, quatro abas estão disponíveis:

#### Tramitações
Timeline completa de todos os andamentos, com:
- Indicação visual de mudanças de regime
- Nome completo do órgão (não apenas a sigla)
- Explicação regimental de cada situação

#### Processo Legislativo
- **Indicador visual de progresso** com 8 fases: Apresentação → Despacho → Comissões → Parecer → Plenário → Votação → Revisão → Sanção/Veto
- **Rito de tramitação** específico para o tipo de proposição (PL, PEC, PLP, MPV, PDL)
- **Explicação do regime** com artigos do RICD, prazos e impacto
- **Explicação da apreciação** com quórum e requisitos
- **Órgão atual** com descrição da competência
- **Último andamento** com data/hora e explicação regimental

#### Estratégia
Até 3 recomendações estratégicas para acelerar a tramitação, cada uma com:
- Ação recomendada
- Base legal (artigos do Regimento Interno)
- Descrição detalhada do procedimento
- Nível de impacto

Exemplos de recomendações:
- Requerer urgência (Art. 153-154 RICD)
- Solicitar celeridade ao relator (Art. 52 RICD)
- Requerer inclusão na Ordem do Dia (Art. 17 RICD)
- Requerer desapensação (Art. 142 RICD)
- Acompanhar admissibilidade de PEC na CCJC (Art. 202 RICD)

#### Documentos
Exibe todos os documentos acessórios vinculados ao projeto, agrupados por tipo:

| Sigla | Tipo | Artigos RICD |
|-------|------|-------------|
| EMC | Emenda de Comissão | Art. 119-120 |
| EMP | Emenda de Plenário | Art. 120 |
| EMD | Emenda (geral) | Art. 118-125 |
| EMS | Emenda do Senado | Art. 65 CF |
| SBT | Substitutivo | Art. 118, §4º |
| REQ | Requerimento | Art. 114-117 |
| REC | Recurso | Art. 132, §2º |
| DTQ | Destaque | Art. 161 |
| RPD | Redação do Plenário | Art. 194 |
| PAR | Parecer | Art. 126-131 |
| VTS | Voto em Separado | Art. 56, §1º |
| ERD | Errata | Art. 126 |
| ESB | Emenda ao Substitutivo | Art. 118 |

Cada tipo traz:
- Descrição e base regimental
- Interpretação contextual (o que significa para aquele projeto específico)
- Subtipos quando aplicável (ex: tipos de destaque, tipos de requerimento)
- Impacto na tramitação

### Notificações

- Detecção automática de mudanças de regime de tramitação
- Detecção de mudanças de tipo de apreciação
- Detecção de novas tramitações
- Notificações push do navegador (quando autorizado)
- Central de notificações com histórico

### Exportação

- Exportar tramitações em CSV
- Link direto para a ficha de tramitação no site da Câmara

### Configurações

- Intervalo de polling configurável (5 a 120 minutos)
- Ativar/desativar notificações push
- Ativar/desativar alerta sonoro para urgências
- **Parlamentar de interesse** — nome usado para identificar relatorias automaticamente
- Modo demonstração com dados simulados

## Tecnologias

- **React 18** — carregado via CDN (sem build step)
- **Babel Standalone** — transpilação JSX no navegador
- **API Dados Abertos da Câmara v2** — `https://dadosabertos.camara.leg.br/api/v2`
- **LocalStorage** — persistência de projetos, notificações, configurações e metadados por proposição (visto/timestamp, prioritário + motivação, votado + data, relatoria). A migração é **retrocompatível**: dados salvos por versões anteriores continuam funcionando — campos ausentes recebem valores padrão automaticamente

## Como Usar

### GitHub Pages (recomendado)

1. Faça fork deste repositório
2. Ative o GitHub Pages nas configurações do repositório (branch `main`, pasta raiz)
3. Acesse `https://seu-usuario.github.io/legis_tracker/`
4. A API da Câmara funciona diretamente do navegador (CORS habilitado)

### Local

Abra o arquivo `index.html` diretamente no navegador. Não requer servidor, build ou instalação de dependências.

```bash
git clone https://github.com/seu-usuario/legis_tracker.git
cd legis_tracker
open index.html  # macOS
# ou
xdg-open index.html  # Linux
```

> **Nota:** A API da Câmara pode bloquear requisições de `file://`. Caso isso ocorra, use um servidor local:
>
> ```bash
> python3 -m http.server 8000
> # Acesse http://localhost:8000
> ```

### Modo Demonstração

O app inicia em modo real (API ativa). Para usar dados simulados sem depender da API, ative o **Modo Demonstração** em Configurações (ícone de engrenagem).

## Estrutura de Arquivos

```
legis_tracker/
├── index.html          # Aplicação completa (HTML + React inline)
├── legis-tracker.tsx    # Código-fonte React (referência)
└── README.md
```

O `index.html` é autossuficiente — contém todo o código da aplicação. O `legis-tracker.tsx` serve como referência do código-fonte antes da integração no HTML.

## API

O LegisTracker consome os seguintes endpoints da [API Dados Abertos da Câmara](https://dadosabertos.camara.leg.br/swagger/api.html):

| Endpoint | Uso |
|----------|-----|
| `GET /proposicoes` | Busca de proposições por tipo, número, ano e autor |
| `GET /proposicoes/{id}` | Detalhes de uma proposição |
| `GET /proposicoes/{id}/tramitacoes` | Histórico de tramitações — também usado para exibir o **último andamento**, extrair o **relator designado** (tramitação de designação), a **data de designação** (marco do prazo de emendas) e detectar votações |
| `GET /proposicoes/{id}/autores` | Autores da proposição |
| `GET /proposicoes/{id}/relacionadas` | Documentos acessórios (emendas, requerimentos, etc.) |
| `GET /deputados` | Busca de deputados por nome |
| `GET /deputados/{id}` | Detalhes do deputado |
| `GET /eventos` | Eventos legislativos (reuniões de comissão e sessões de Plenário) no intervalo de hoje a +7 dias |
| `GET /eventos/{id}/pauta` | Proposições em pauta de cada evento — base das **notas de pauta** e do badge "Em pauta" |

## Base Regimental

O motor de análise estratégica referencia os seguintes dispositivos do Regimento Interno da Câmara dos Deputados:

- **Regimes de tramitação** — Art. 151-155 (ordinário, prioridade, urgência, urgência urgentíssima)
- **Emendas** — Art. 118-125 (tipos, prazos, procedimento)
- **Prazo de emendas em comissão** — **Art. 119, I** (5 sessões a partir da designação do relator). É a base usada pelo LegisTracker para a contagem do prazo de emendas: a `dataHora` da tramitação de designação do relator é o marco inicial, e o app exibe a data de designação, a base regimental e uma estimativa do fim do prazo em dias úteis. *Observação: o Art. 166 do RICD não se aplica a emendas em comissão — refere-se apenas ao caso específico de discussão encerrada na fase de Plenário, e por isso não é citado nessa contagem*
- **Pareceres** — Art. 126-131 (elaboração, votação, efeitos)
- **Poder conclusivo** — Art. 24, II e Art. 58, §2º, I CF (comissões com poder terminativo)
- **Recursos** — Art. 132, §2º (recurso contra poder conclusivo)
- **Destaques** — Art. 161 (votação em separado)
- **Requerimentos** — Art. 114-117 (urgência, audiência, adiamento)
- **PECs** — Art. 191-202 (comissão especial, admissibilidade, dois turnos)
- **Prazos** — Art. 52 (parecer do relator: 10 sessões em regime ordinário, 5 em prioridade/urgência — exibido na visão de Relatorias), Art. 202 (comissão especial)

## Licença

MIT
