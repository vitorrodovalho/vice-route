# 02. Dados e referências

O valor do produto depende de um catálogo completo e correto. Este documento define de onde vêm os dados, como entram no catálogo e como a confiança de cada informação é registrada. O formato do catálogo está em [04-grafo-progressao.md](04-grafo-progressao.md) e o processo de contribuição em [08-comunidade-curadoria.md](08-comunidade-curadoria.md).

## 1. Referências levantadas (23/11/2026)

### 1.1 Vice Atlas (vice-atlas.com)

Site independente de notícias e guias de GTA VI, construído em Astro e servido como site estático. Ele oferece três coisas úteis:

**a) Dados do mapa em JSON público e estático:** `https://www.vice-atlas.com/map-data/leonida.json` ("Map Data Schema v1", ~210 KB, atualizado em 23/11/2026).

| Bloco | Conteúdo |
|---|---|
| `basemap` | Imagem-base usada, com créditos, tamanho (5775 × 7900 px), recorte (`x 200, y 200, 5376 × 7500`) e caminho dos tiles |
| `vocab` | Categorias (`places` 9, `vehicles` 82, `weapons` 88), qualidade de posição e o que o pino representa |
| `entries` | 170 pinos |
| `places`, `place_markers`, `claims`, `retired`, `coverage` | Lugares com página no site, alegações referenciadas, entradas removidas e cobertura |

Cada entrada traz:

```
key, name, category, subcategory, group, hub (link da página), area, directions (texto),
position { quality: verified|approximate, represents: point|entrance|search_area|…,
           anchor, anchor_name, relation, x, y, basis },
level, availability (always|conditional|…), conditions { prerequisites[] },
game_context { platforms, version, mode }, progress { action, set },
spoiler_level, spoiler_gated, evidence { confirmation, source }
```

As posições são **pixels da imagem-base original** (origem no canto superior esquerdo, y para baixo), nunca coordenadas do jogo nem latitude/longitude. Esse é o sistema adotado pelo Vice Route (ver §5 e [ADR 003](adr/003-coordenadas-imagem-base.md)), então o importador não precisa de transformação de coordenadas.

**b) Guia de colecionáveis** (`/collectibles/`): tipos, totais e o que o checklist de 100% exige, cada linha classificada por confiança (no levantamento, todas como "inferência, alta confiança", com base no checklist de 100% do PowerPyx). Não traz posições.

**c) Detonados de missões** (`/briefings/gta6-mission-NN-…`): missão a missão, com número, capítulo, nível de spoiler e alegações classificadas. É uma boa base para a espinha da campanha.

**O que o Vice Atlas não tem:** os pinos de colecionáveis e atividades (cartas, flamingos, WAiNK, saltos, voos de faca…). O próprio guia lista "onde está cada carta, flamingo e ponto WAiNK" como pergunta em aberto.

### 1.2 Colecionáveis e o que conta para 100%

Segundo o guia do Vice Atlas e o checklist do PowerPyx (status `referencia`, a confirmar):

| Categoria | Total | Exigido para 100% | ID proposto | Observação |
|---|---:|---:|---|---|
| Cartas colecionáveis | 480 (18 conjuntos de 18 a 36) | 15 conjuntos completos | `cartas` | Três conjuntos dão um troféu |
| Flamingos de cocaína | 125 | 100 | `flamingos` | A droga que caem pode ser vendida |
| Locais WAiNK | ~112 (31 marcos, 35 curiosidades, 23 esculturas e outros) | 100 | `waink` | Total ainda divergente entre guias |
| Descobertas subaquáticas | 33 (7 naufrágios) | 33 | `subaquaticas` | Liberadas depois da missão *Island Dreams* (cap. 1) |
| Revistas, livros e quadrinhos | 27 + 7 trocas de livros = 34 | 30 | `revistas-livros` | Só uma cópia de cada título conta |
| Pontos de meditação | 18 (10 lugares, Jason e Lucia) | 15 | `meditacao` | **Perdível:** um fica numa casa usada só no início do capítulo 1 |
| Estilos descobertos | 79 (13 Jason, 15 Lucia, 17 tatuagens, 34 relógios) | 70 | `estilos` | |
| Playlists únicas | 2 | 2 | `playlists` | Liberadas escaneando cartazes com WAiNK |

**Atividade de sinuca:** vencer 5 dos 7 adversários distintos conta para os 100%. O contador fica em **Progress > Activities > Sports > Pool**. Se o Delights Cabaret estiver temporariamente fechado, outro adversário ainda não vencido pode contar para a mesma meta. Referência: [guia de adversários de sinuca do Vice Keys](https://getvicekeys.com/guides/gta-6-pool-opponent-locations).

Outros fatos levantados:
- O jogo conta colecionáveis em **Progresso > Coleções**, e um tipo costuma aparecer só depois de achar o primeiro.
- O **checklist de 100%** abre depois da história, conta o que foi achado antes e pede só parte da maioria dos conjuntos.
- Voos de faca aparecem em Progresso > Acrobacias e como "Stunt Spots" no mapa do jogo depois do primeiro.
- A campanha tem **capítulos explícitos** e missões numeradas (ex.: missão 5 *Lab Fees*, 9 *Island Dreams*, 14 *Down To Earth*).

Consequência para o modelo: "100%" não é a soma de todos os itens. Cada categoria tem uma **regra de 100%** (mínimo de itens ou de conjuntos completos), e o contador precisa mostrar os dois números: o total coletado e o exigido (ver [04-grafo-progressao.md](04-grafo-progressao.md) §1).

### 1.3 A captura usada como ponto de partida

A captura inicial (tela "100% do jogo", "0 / 901 achados", busca "Buscar em Leonida") **não é do Vice Atlas**. As categorias dela batem com o conjunto de dados aberto do **Open GTA 6 Map** (§1.5): 202 "sob a ponte", 130 saltos acrobáticos (131 na captura), 40 voos de faca, 25 saltos BASE, 5 murais 1010, 5 tesouros (as "descobertas subaquáticas"). Provavelmente é um app independente que montou o catálogo a partir desses dados públicos, num recorte de 901 itens.

### 1.4 Onde a comunidade publica os achados (levantamento de 23/11/2026)

Quatro dias depois do lançamento (19/11/2026), boa parte do que o projeto precisa já está publicada. O que importa é a licença: só conjuntos com licença aberta são importados; o resto serve de referência para conferência e levantamento assistido, sempre com `fontes[]`.

| Tipo de dado | Fonte | Formato | Licença | Uso no Vice Route |
|---|---|---|---|---|
| **Pinos de colecionáveis, atividades, crimes e serviços** | [Open GTA 6 Map](https://github.com/fbritoferreira/gta6) (`public/data/locations.json`, `categories.json`) | JSON, 3.827 locais, 105 tipos | **CC BY 4.0** | **Importação principal** (spec 002) |
| Regras de 100%, capítulos, limiares | [GTA6Maps dataset](https://github.com/duanhesen/gta6-leonida-map-data) (`completion-100-thresholds.json`) | JSON | **CC BY 4.0** | Importação de `categorias.yaml` e `capitulos.yaml` |
| Marcos, negócios e atividades | GTA6Maps dataset (`gta-6-markers.json`) | JSON/CSV, 798 pinos | CC BY 4.0 | Conferência cruzada de posições |
| Veículos, armas e lugares | Vice Atlas (`map-data/leonida.json`) | JSON, 170 pinos | Não declarada | Referência; importação só com crédito e para conferência |
| Lista de missões principais (91) e detonados | [PowerPyx](https://www.powerpyx.com/gta-6-full-walkthrough-all-missions/), [GTAbites](https://www.gtabites.com/missions), detonados do Vice Atlas | Texto | Proprietária | Levantamento assistido da espinha da campanha |
| Strangers & Freaks (14 personagens, 39 missões) | [PowerPyx](https://www.powerpyx.com/gta-6-all-strangers-freaks-side-missions/), [games.gg](https://games.gg/grand-theft-auto-vi/guides/grand-theft-auto-6-side-missions-list-strangers-and-freaks-questlines/), [outputlag](https://outputlag.com/guides/gta-6-side-missions-strangers-freaks/), [gmtreks](https://gmtreks.com/gta6/page/01M7QVGK6BDTA8232FS0MEXMBE) | Texto | Proprietária | Levantamento assistido por capítulo |
| Perdíveis e pontos sem retorno | [outputlag](https://outputlag.com/guides/gta-6-missables/), [vicemissions](https://vicemissions.com/guides/points-of-no-return), [game-checklists](https://game-checklists.com/gta6/guides/missables/), [metaforge cap. 1](https://metaforge.app/gta-6/gta-6-chapter-1-missables-point-of-no-return) e [cap. 2](https://metaforge.app/gta-6/gta-6-chapter-2-missables-point-of-no-return), [vifieldguide](https://vifieldguide.com/guides/missables/), [thegameswiki](https://thegameswiki.com/gta6/wiki/missable-content-and-points-of-no-return) | Texto | Proprietária | Janelas `perdivel_apos`, cenários-ouro |
| Checklist completo de 100% (2.634 itens em 37 listas) | [Game Checklists](https://game-checklists.com/gta6/), [GTAside](https://gtaside.com/tips/gta6/100-percent-completion/), [gta6fieldmap](https://gta6fieldmap.com/checklist) | Texto | Proprietária | Conferência de totais e categorias |
| Acrobacias (397: 130 saltos, 202 sob pontes, 40 voos de faca, 25 BASE) | [gmtreks](https://gmtreks.com/gta6/page/01M7SG8BDF99FKAZ03AWPFH0XA), [egamers](https://egamers.io/gta-6-stunt-guide-all-397-stunts-the-cunning-stunts-trophy-and-the-100-target/), [tracker.gg](https://tracker.gg/gta6/map/knife-flights) | Texto e mapa | Proprietária | Conferência |
| Troféus (55) | [platinum-route-trophy-guide](https://github.com/NaufalDzz/platinum-route-trophy-guide) (`trophies.json`), PowerPyx | JSON | Não declarada | Referência para `trofeu` nos nós |
| Mapas interativos completos | [GTABase](https://www.gtabase.com/gta-6/map/) (958 locais, 43 categorias), [gtasixmap](https://www.gtasixmap.com/) | Web | Proprietária | Conferência visual |
| Enciclopédia de fã | [gta6-hq](https://github.com/xpbeats00-sudo/gta6-hq) (`data/wiki/*.json`) | JSON | Sem licença | Só leitura de referência |

Fóruns e Reddit não apareceram com listas estruturadas nesta busca. Os sites de guia e os repositórios acima já consolidam o que foi descoberto, quase sempre citando PowerPyx.

### 1.5 Open GTA 6 Map: a fonte principal de pinos

- **Projeto:** mapa aberto mantido no GitHub (fbritoferreira/gta6), publicado no GitHub Pages, com dados, tiles e material próprio sob **CC BY 4.0**. É possível compartilhar e adaptar, inclusive comercialmente, com crédito, link da licença e indicação de alterações.
- **`locations.json`:** 3.827 locais. Campos: `id` estável (ex.: `hidden-package-001`, `magazine-009`), `kind` (categoria), `type` (rótulo), `x`, `y` em unidades do mapa, `name`, `description`, `screenshot` (610 com captura), `area`, `region`, `confidence` (3.097 `community`, 730 sem valor) e `source_urls` (vazio em todos no levantamento).
- **IDs removidos:** `removed.json` lista IDs retirados de propósito, com motivo, e o build deles falha se um reaparecer. Isso combina com o nosso princípio 7.
- **Coordenadas:** unidades próprias (x de 222 a 599, y de 217 a 772), com Leaflet `CRS.Simple` e latitude = −y. O contorno de terra deles foi **traçado sobre o mapa imprimível da Rockstar**, a mesma imagem-base que adotamos (ADR 003). A conversão para pixels da nossa imagem é uma **transformação afim**, calculada com pontos de controle presentes nas duas bases e checada pelo resíduo.
- **Tiles deles:** 17.986 arquivos e ~200 MB (temas dia e noite, zoom 0 a 5). Não copiamos; mantemos a nossa pirâmide de ~20 MB gerada da imagem oficial.
- **Cobertura relevante** (contagens de 23/11/2026):

| Tipo no Open GTA 6 Map | Qtde | | Tipo | Qtde |
|---|---:|---|---|---:|
| Under the bridge | 202 | | Hidden package | 76 |
| Stunt jump | 130 | | Magazine (+ comics, book swap) | 73 + 4 + 7 |
| Basketball hoop | 100 | | Cartas (18 conjuntos + 17 avulsas) | 371 |
| Towable ATM | 93 | | Meteorite | 51 |
| Yellow Loot Box | 81 | | Ahol Street Art | 50 |
| WAiNK (landmark, curiosity, sculpture) | 31 + 41 + 23 | | Drone race | 42 |
| Knife flight | 40 | | Gang operation | 41 |
| BASE jump | 25 | | Smash 'n' Grab | 35 |
| Safe | 62 | | Bank | 22 |
| Meditation spot | 10 | | Treasure | 5 |
| Unique Weapon / Weapon pickup | 52 / 65 | | 1010 mural | 5 |

Cartas, flamingos e WAiNK ainda estão abaixo dos totais do jogo (480 cartas, 125 flamingos), então a cobertura cresce com a comunidade. O importador re-executável (spec 002) acompanha essas atualizações.

### 1.6 Regras de 100% e capítulos (GTA6Maps, CC BY 4.0)

O checklist de 100% do jogo abre depois das 91 missões principais (Mapa > Progresso > Estatísticas). Limiares publicados:

| Categoria | Exigido / total | | Categoria | Exigido / total |
|---|---:|---|---|---:|
| Missões principais | 91 / 91 | | Acrobacias combinadas | 300 / 397 |
| Operações de gangue | 39 / 39 | | Descobertas subaquáticas | 33 / 33 |
| Trabalhos de banco | 22 / 22 | | Revistas, livros e quadrinhos | 30 / 30 |
| Empréstimos (Payday Loan) | 7 / 7 | | Pontos de meditação | 15 / 15 |
| Smash 'n' Grab | 34 / 34 | | Veículos escaneados (WAiNK) | 200 / 557 |
| Assaltos a lojas de conveniência | 50 / 50 | | Animais estudados (binóculo) | 100 / 255 |
| Conjuntos de cartas | 15 / 18 | | Caçadas do Bret | 15 / 15 |
| Flamingos de cocaína | 100 / 125 | | Cestas de basquete | 90 / 90 |
| Escaneamentos WAiNK de marcos | 100 / 100 | | Provas de drone | 42 / 42 |
| | | | Estandes de tiro (ouro) | 42 / 42 |

Capítulos: prólogo (Vice-Dale), 1 *Just Like Paradise* (~22 missões, Leonida Keys, casa de palafita do Jason em Key Lento), 2 *Paradise Lost* (~11, Port Gellhorn, Starlet Motel), 3 *Tits, Ass, Drug Money* (~26, Vice City, Bayside Apartments), 4 *Vice City, Baby* (~25, Vice City e Ocean Beach, suíte do Y Hotel), 5 *Top of the World, Ma* (~7).

**Divergências entre fontes** (resolver no levantamento, registrando as duas em `fontes[]`):
- O GTA6Maps diz `missable_items: false`, mas vários guias de perdíveis listam itens e missões perdíveis: as duas missões da Shirley somem ao iniciar *Tourist Trap*, o revólver do Donnie some depois de *Don Gone*, e o troféu de meditação pode ser perdido ao recusar o convite da Lucia depois de *Down To Earth*. Os pontos sem retorno citados são: **capítulo 1**, *Tourist Trap* (alguns guias dizem *Recalibration*), com perda do dinheiro e da casa em Key Lento; **capítulo 2**, *Checking Out*, com perda de revistas e cartas do motel.
- Meditação: 15 / 15 (GTA6Maps) contra 15 / 18 (PowerPyx, contando Jason e Lucia separados).
- Revistas: 30 / 30 contra 30 / 34 (com as trocas de livros).
- WAiNK: 100 / 100 contra ~112 escaneáveis.
- Missões principais por capítulo: os guias contam de jeitos diferentes (22/11/26/25/7 contra 25/18/31/34/10).

### 1.7 Levantamento de missões (23/11/2026)

Feito com pesquisa assistida em guias públicos e gravado por `pnpm levantamento:missoes` (primeira passada; depois disso o YAML é editado à mão). Cada nó cita as próprias fontes.

| O quê | No catálogo | Base |
|---|---:|---|
| Missões principais | 91, em ordem, com capítulo e personagem | Lista idêntica em gtabites, Dexerto e KeenGamer; personagem por Vice Atlas, GTABase e PowerPyx |
| Missões secundárias | 45 em 23 linhas (Strangers & Freaks e outras) | PowerPyx, outputlag, GTABase, retrobroker, games.gg |
| Missões ocultas e gatilhos | 8 ocultas, 8 gatilhos | Freddie, Dr. Merle, Alvan West, Papa Audacity, Kirk, Kyle, Shayna e o encontro com OVNI |
| Janelas de perda | 4 pontos sem retorno com aviso na missão; 10 itens e 7 missões perdíveis | outputlag, vicemissions, Game Checklists, vifieldguide, PowerPyx |

Pontos sem retorno: *Tourist Trap* (início, cap. 1), *Checking Out* (início, cap. 2; fontes divergem se é na conclusão), *The Cartel Heist* (conclusão, cap. 3; contestado), *Sunset* (cap. 4) e *Protective Custody* (cap. 5). Perdas que não são nós (dinheiro, casa em Key Lento, Ganado) estão como dica na missão que as fecha.

Pendências do levantamento:
- 91 ou 93 missões principais (duas do Billy aparecem em algumas listas); *Tyrone* pode ser objetivo de *Here, Kitty Kitty*; *Clonin' Keys, Makin' Gs* tem nome alternativo.
- A ordem está linear; trechos que podem ser feitos em qualquer ordem (cap. 1 #4–7, estratos do Jason e da Lucia nos cap. 3 e 4) ainda não foram modelados.
- Não confirmado se *Protective Custody* e *The End of the Road* são finais alternativos.
- Cópias de *Body Copy* e *Off the Hook* na casa do Jason, revistas do Starlet Motel (*Help Me Help You Help Me*, *The Wizard Wang*) e a carta do Victor Maravilla no motel ainda não foram identificadas no catálogo.
- Missões com tempo limitado (*Race Against Yago*, *This is a Free Call From...*) estão como instrução, sem janela de perda, porque nenhuma fonte dá o prazo.
- Quais 8 linhas contam para o troféu Strangers & Freaks: fontes divergem; não modelado.
- Dodo Rising (protestos) ficou de fora: missões sem nome nas fontes.

## 2. Fontes e nível de confiança

Cada nó e cada aresta do catálogo carrega `fontes[]` e um `status` de confiança.

| Fonte | Tipo (`fonte.tipo`) | Para quê | Confiança inicial |
|---|---|---|---|
| Open GTA 6 Map e GTA6Maps (CC BY 4.0) | `aberto` | Pinos de colecionáveis e atividades; regras de 100%; capítulos | `referencia` (`nao_confirmado` quando `confidence` vier vazio) |
| Vice Atlas | `referencia` | Veículos, armas e lugares; detonados; conferência | `referencia` |
| Lista oficial de troféus do jogo | `trofeu` | Esqueleto de categorias e totais; nomes oficiais | `oficial` |
| Material oficial da Rockstar (site, guia, mapa do jogo) | `oficial` | Nomes de regiões, personagens, missões | `oficial` |
| Wikis e guias da comunidade (Fandom, GTABase e similares) | `referencia` | Missões, gatilhos, perdíveis, requisitos | `referencia` |
| Vídeos de detonado | `referencia` | Confirmar ordem de missões e gatilhos | `referencia` |
| Contribuição no app ou por PR | `comunidade` | Correções, itens novos, gatilhos descobertos | `nao_confirmado` |
| Jogado e verificado por um curador | `jogado` | Confirmação final | `confirmado` |

Escala de `status`:

```
nao_confirmado → referencia → confirmado
                       ↘ contestado (há reporte de erro aberto)
                       ↘ depreciado (item removido ou fundido; ID preservado)
```

Regras:
- O `status` do catálogo só muda por PR (princípio 5). Um nó vira `confirmado` com fonte `jogado`, com fonte `oficial`, ou quando atinge N confirmações independentes de jogadores no app: nesse caso uma Action abre o PR de promoção (N configurável; proposta inicial N = 3; ver [08-comunidade-curadoria.md](08-comunidade-curadoria.md)).
- `contestado` no catálogo é decisão do curador. Reporte aberto no app **não** altera o catálogo: a interface mostra um selo "reporte em análise" lido da view pública agregada de reportes, sem esconder o item.
- A interface mostra selo para `nao_confirmado`, `contestado` e "reporte em análise".

## 3. Levantamento inicial

| Etapa | Saída | Como |
|---|---|---|
| 1. Categorias, totais, regras de 100% e capítulos | `data/categorias.yaml`, `capitulos.yaml` | Importador do `completion-100-thresholds.json` do GTA6Maps (§1.6), conferido com PowerPyx e GTAside |
| 2. Pinos de colecionáveis, atividades e crimes | `data/itens/<categoria>.yaml` | **Importador do Open GTA 6 Map** (§1.5), com transformação afim para a imagem-base (spec 002) |
| 3. Veículos, armas e lugares | `data/itens/{veiculos,armas,lugares}.yaml` | Importador do JSON do Vice Atlas, para conferência e para preencher lacunas |
| 4. Espinha da campanha | `data/missoes/principais.yaml` | 91 missões numeradas por capítulo, a partir do detonado completo do PowerPyx, do GTAbites e dos detonados do Vice Atlas |
| 5. Secundárias e ocultas | `data/missoes/secundarias.yaml`, `ocultas.yaml` | Wikis e guias; cada uma com início, personagem e requisitos |
| 6. Gatilhos | `data/gatilhos.yaml` | Guias de missões ocultas: o que fazer, onde e quando |
| 7. Janelas e perdíveis | Campos `disponivel_desde` e `perdivel_apos` | Guias de perdíveis do §1.4 (pontos sem retorno: *Tourist Trap* no cap. 1, *Checking Out* no cap. 2) |
| 8. Durações | Campo `duracao_min` | Estimativa por tipo; depois, mediana da comunidade (spec 014) |

O levantamento das etapas 3 a 7 é manual e assistido: pesquisa em fontes públicas, transcrição para YAML e revisão. Um agente de codificação (Claude Code) pode fazer a primeira passada de pesquisa e transcrição, sempre registrando a URL de origem em `fontes[]` e marcando como `referencia`.

## 4. Mapa e imagens

- **Imagem-base:** o mapa oficial de Leonida publicado pela Rockstar (pôster impresso, `rockstargames.com/VI/media`, 19/11/2026, 5775 × 7900 px). É a mesma base do Vice Atlas. Limites conhecidos: é um pôster, não o mapa do jogo; ruas desenhadas sem nomes; rótulos posicionados para leitura.
- **Tiles:** gerados por script próprio a partir da imagem original (não se copia nem se aponta para os tiles do Vice Atlas, princípio 8). Pirâmide `z/x/y.webp` de 512 px, zoom nativo 0 a 4 e zoom de exibição até 5, cerca de 230 tiles e 10 a 20 MB no total. Ficam versionados com o app e são servidos pela CDN da Vercel (ver [10-decisoes-riscos.md](10-decisoes-riscos.md), D8).
- **Edições da imagem-base:** cada imagem tem um `id` (ex.: `leonida-poster-2026-11-19`). Se surgir base melhor (por exemplo, captura completa do mapa do jogo), ela entra como nova edição com transformação afim a partir da anterior, e as posições são migradas por script.
- **Ícones de categoria:** SVG próprios, um por categoria, com a cor da categoria.
- **Imagens de itens** (opcional): captura do local, hospedada fora do repositório para não inflar o histórico do git.

## 5. Convenções de dados

- Posições sempre em **pixels da imagem-base** indicada em `data/mapa.yaml` (origem no canto superior esquerdo, y para baixo), nunca latitude/longitude. Cada posição traz `qualidade` (`verificada` | `aproximada`) e `representa` (`ponto` | `entrada` | `area_busca`), como no Vice Atlas.
- Nomes em português do Brasil, com `nome_original` em inglês quando houver.
- IDs em kebab-case, prefixados pelo tipo: `item:cartas-017`, `missao:principal-012`, `gatilho:loja-x-noite`, `regiao:vice-city`.
- Toda alteração em `data/` passa pela validação de esquema e pelos cenários-ouro do motor no CI.

## 6. Créditos e licenças

- Dados sob CC BY 4.0 (Open GTA 6 Map, GTA6Maps) exigem crédito, link da licença e indicação de alterações. O app tem uma página **Créditos** com cada fonte, licença e o que foi alterado (tradução, conversão de coordenadas, fusão), e o mapa mostra um crédito curto no canto.
- O arquivo `data/FONTES.md` e o campo `fontes[]` de cada nó registram a origem. O importador grava a versão (commit) da fonte usada.
- Fontes sem licença declarada (Vice Atlas, gta6-hq, sites de guia) não são copiadas em massa: servem para conferência e levantamento assistido, com link em `fontes[]`.
- Imagem-base: mapa oficial da Rockstar, com crédito à Rockstar Games.

## 7. Itens não confirmados nesta pesquisa

- Troca entre Jason e Lucia: livre, por missão ou por capítulo. O modelo de dados aceita os três casos; ver [04-grafo-progressao.md](04-grafo-progressao.md).
- Totais divergentes entre fontes (§1.6) e cobertura ainda incompleta de cartas e flamingos no Open GTA 6 Map.
- Missões principais exatas por capítulo e ponto sem retorno do capítulo 1 (*Tourist Trap* ou *Recalibration*).
- Pontos de controle e resíduo da transformação afim entre o Open GTA 6 Map e a imagem-base.
- Regra exata de cálculo da porcentagem oficial do jogo.
