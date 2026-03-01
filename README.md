# LegisTracker

Dashboard de monitoramento de proposições legislativas da Câmara dos Deputados do Brasil, com análise estratégica baseada no Regimento Interno (RICD).

![React](https://img.shields.io/badge/React-18-blue)
![API](https://img.shields.io/badge/API-Dados%20Abertos%20C%C3%A2mara-green)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

## Visão Geral

O LegisTracker permite acompanhar em tempo real a tramitação de projetos de lei na Câmara dos Deputados, oferecendo:

- Atualização automática dos andamentos de cada proposição monitorada
- Análise estratégica com recomendações baseadas no Regimento Interno para acelerar a tramitação
- Interpretação contextualizada de documentos acessórios (emendas, requerimentos, recursos, destaques)
- Notificações de mudanças de regime, apreciação e novas tramitações

## Funcionalidades

### Dashboard

- **Painel de resumo** com contadores de projetos em urgência, plenário, comissão e total monitorado
- **Cards de projeto** exibindo para cada proposição:
  - Último andamento com data/hora
  - Regime de tramitação (Urgência, Prioridade, Ordinário) e tipo de apreciação (Plenário, Comissão, Poder Conclusivo)
  - Rito processual que o projeto seguirá
  - Recomendação estratégica principal para acelerar a tramitação, com base legal no RICD
- **Filtros e ordenação** por tipo, ano, regime, apreciação e andamento mais recente
- **Modo escuro/claro**

### Busca e Monitoramento

- **Busca por proposição** — formato `PL 1234/2023`, `PEC 45/2019`, etc. Tipos suportados: PL, PEC, PDL, PLP, MPV, PLV, PRC, PDC, MSC, PLN
- **Busca por deputado** — pesquisa pelo nome e exibe todas as proposições como autor/coautor ou relator
- **Monitorar Todos** — botão para adicionar de uma vez todas as proposições de um deputado ao monitoramento (centenas de projetos)
- **Carregar Todas as Proposições** — paginação automática para buscar o acervo completo de um deputado

### Detalhes do Projeto

Ao clicar em um projeto monitorado, quatro abas estão disponíveis:

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
- **Situação** com explicação regimental

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
- Modo demonstração com dados simulados

## Tecnologias

- **React 18** — carregado via CDN (sem build step)
- **Babel Standalone** — transpilação JSX no navegador
- **API Dados Abertos da Câmara v2** — `https://dadosabertos.camara.leg.br/api/v2`
- **LocalStorage** — persistência de projetos, notificações e configurações

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
| `GET /proposicoes/{id}/tramitacoes` | Histórico de tramitações |
| `GET /proposicoes/{id}/autores` | Autores da proposição |
| `GET /proposicoes/{id}/relacionadas` | Documentos acessórios (emendas, requerimentos, etc.) |
| `GET /deputados` | Busca de deputados por nome |
| `GET /deputados/{id}` | Detalhes do deputado |

## Base Regimental

O motor de análise estratégica referencia os seguintes dispositivos do Regimento Interno da Câmara dos Deputados:

- **Regimes de tramitação** — Art. 151-155 (ordinário, prioridade, urgência, urgência urgentíssima)
- **Emendas** — Art. 118-125 (tipos, prazos, procedimento)
- **Pareceres** — Art. 126-131 (elaboração, votação, efeitos)
- **Poder conclusivo** — Art. 24, II e Art. 58, §2º, I CF (comissões com poder terminativo)
- **Recursos** — Art. 132, §2º (recurso contra poder conclusivo)
- **Destaques** — Art. 161 (votação em separado)
- **Requerimentos** — Art. 114-117 (urgência, audiência, adiamento)
- **PECs** — Art. 191-202 (comissão especial, admissibilidade, dois turnos)
- **Prazos** — Art. 52 (relator), Art. 202 (comissão especial)

## Licença

MIT
