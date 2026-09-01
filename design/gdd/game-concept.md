# Game Concept: Nó Vivo

*Created: 2026-09-01*
*Status: Draft*

---

## Elevator Pitch

> É um puzzle tátil mobile onde você desemaranha fios de lã com física de tensão real — puxar na ordem errada trava o nó — e cada novelo resolvido constrói permanentemente uma Vila de criaturas de lã.
>
> Test: uma pessoa que nunca ouviu falar do jogo entende em 10 segundos o que estaria fazendo? Sim — "puxar fios sem errar a ordem, construir uma vila."

---

## Core Identity

| Aspect | Detail |
| ---- | ---- |
| **Genre** | Puzzle casual / Sort-Untangle + Collection-Builder |
| **Platform** | Mobile (iOS / Android) |
| **Target Audience** | Jogadores casuais de puzzle relaxante (público de "Destress") com apelo secundário a completistas/colecionadores |
| **Player Count** | Single-player |
| **Session Length** | 5-15 minutos, múltiplas sessões por dia |
| **Monetization** | F2P — ads recompensados (continuar/bônus) + IAP cosmético para a Vila (sem pay-to-win) |
| **Estimated Scope** | Large (9–14 months to full-featured launch, solo dev; live-ops content continues indefinitely post-launch) |
| **Comparable Titles** | Wool Crush / Wool Sort 3D, Merge Mansion, Flow Free |

---

## Core Fantasy

A satisfação tátil de desfazer o caos com as próprias mãos — sentir que você "lê" o nó antes de agir e é recompensado por isso — combinada com o orgulho tranquilo de ver algo bonito e permanente crescer a partir desse esforço. Não é sobre pressão ou competição: é sobre a certeza calma de "eu consigo resolver isso", seguida da alegria de ver a Vila florescer com cada vitória.

---

## Unique Hook

É como um jogo de wool-sort (Wool Crush, Wool Sort 3D), E TAMBÉM os fios têm física real de corda — a ordem em que você puxa importa de verdade, e escolher errado pode travar o nó, exigindo desfazer. Isso transforma um gênero de "toque aleatório e relaxe" em um puzzle genuíno com solução ótima, sem perder a sensação sensorial que torna o gênero viciante. O diferencial afeta a jogabilidade central, não é só um verniz visual.

---

## Player Experience Analysis (MDA Framework)

### Target Aesthetics (What the player FEELS)

| Aesthetic | Priority | How We Deliver It |
| ---- | ---- | ---- |
| **Sensation** (sensory pleasure) | 1 | Física elástica dos fios, áudio em camadas (boing, acorde ascendente, estalo), partículas de lã |
| **Fantasy** (make-believe, role-playing) | N/A | — |
| **Narrative** (drama, story arc) | N/A | — |
| **Challenge** (obstacle course, mastery) | 3 | Risco real de travar, Nós-História cadenciados, sistema de combo |
| **Fellowship** (social connection) | N/A | Fora do MVP — possível expansão futura (visitar vilas de amigos) |
| **Discovery** (exploration, secrets) | 4 | Desbloqueio progressivo de construções e criaturas na Vila |
| **Expression** (self-expression, creativity) | 5 | Customização cosmética da Vila |
| **Submission** (relaxation, comfort zone) | 2 | Paleta quente, ausência de pressão de tempo, sem punição severa |

### Key Dynamics (Emergent player behaviors)

- Jogadores vão desenvolver leitura espacial rápida, planejando a ordem de puxada antes de agir em vez de tocar aleatoriamente
- Jogadores vão voltar para checar o progresso visual da Vila mesmo em sessões onde só querem "dar uma olhada"
- Jogadores mais engajados vão buscar ativamente os Nós-História pelo desafio, enquanto o público casual pode ignorá-los sem prejuízo

### Core Mechanics (Systems we build)

1. Física de corda com tensão real — puxar fios com risco genuíno de travamento e undo
2. Sistema de combo por ordem de resolução — bônus visual e de recompensa por sequências ótimas
3. Geração procedural de nós com validação de solubilidade
4. Meta de coleção/construção permanente — Vila de Lã alimentada por lã coletada
5. Nós-História — picos de dificuldade curados, cadenciados a cada ~10 níveis

---

## Player Motivation Profile

### Primary Psychological Needs Served

| Need | How This Game Satisfies It | Strength |
| ---- | ---- | ---- |
| **Autonomy** (freedom, meaningful choice) | Escolha da ordem de puxada, qual novelo atacar primeiro, customização da Vila | Core |
| **Competence** (mastery, skill growth) | Leitura espacial melhora visivelmente — menos "desfazer" usados, combos mais longos ao longo do tempo | Core |
| **Relatedness** (connection, belonging) | Criaturas da Vila com personalidade leve, colecionáveis | Supporting |

### Player Type Appeal (Bartle Taxonomy)

- [x] **Achievers** (goal completion, collection, progression) — Como: completar Bobinas e ver a Vila crescer é o principal motor de retorno
- [x] **Explorers** (discovery, understanding systems, finding secrets) — Como: descobrir novas mecânicas de nó (cordas travadas/congeladas) e novos biomas
- [ ] **Socializers** (relationships, cooperation, community) — Fora do MVP
- [ ] **Killers/Competitors** (domination, PvP, leaderboards) — Explicitamente excluído (anti-pilar)

### Flow State Design

- **Onboarding curve**: o primeiro nó tem só 2 fios e é impossível de travar — ensina o gesto de puxar sem risco; o segundo introduz o risco leve de travamento
- **Difficulty scaling**: fios aumentam gradualmente em número e cruzamento; mecânicas novas (cordas travadas/congeladas) só entram depois do nível 20
- **Feedback clarity**: menos usos de "desfazer" e combos mais longos são indicadores visíveis e imediatos de habilidade crescente
- **Recovery from failure**: desfazer é instantâneo e sem custo de progresso — nunca perde a Bobina inteira, só reseta o nó atual; falha é educativa, não punitiva

---

## Core Loop

### Moment-to-Moment (30 seconds)

Puxar pontas de fios emaranhados com física elástica real. Escolher a ordem errada pode travar o nó (exigindo desfazer); a ordem certa libera o fio com feedback sonoro e visual satisfatório (boing, acorde ascendente, partículas de lã ao limpar uma cor).

### Short-Term (5-15 minutes)

Um "novelo" (nó individual) leva 10-20 segundos; vários novelos formam uma "Bobina" (nível, 3-8 mini-puzzles). Completar uma Bobina rende Lã, que constrói visivelmente um prédio na Vila — o gancho de "só mais um pouco para terminar a casa" puxa a próxima rodada.

### Session-Level (30-120 minutes)

Várias Bobinas completadas resultam em um marco visual na Vila. A cada ~10 níveis surge um Nó-História — puzzle curado, propositalmente difícil. Parada natural ao fim de cada Bobina; o retorno é motivado pela construção inacabada da Vila e por um "nó especial do dia".

### Long-Term Progression

O jogador cresce em três eixos: a Vila (coleção visual permanente), a habilidade espacial (nós ficam genuinamente mais complexos ao longo do tempo) e novos biomas de Vila (conteúdo sazonal, alimentando a visão de live-ops multi-anos). Não há "fim" tradicional — é um jogo-serviço com Vila sempre expansível.

### Retention Hooks

- **Curiosity**: qual construção desbloqueia a seguir na Vila
- **Investment**: a Vila que já construíram — vínculo emocional com o progresso visível
- **Social**: fora do MVP (expansão futura — visitar vilas de amigos)
- **Mastery**: Nós-História, combos, redução de "desfazer" necessários ao longo do tempo

---

## Game Pillars

### Pillar 1: Tensão Tátil Real

Cada nó tem risco físico genuíno — a ordem errada trava, a ordem certa flui.

*Design test*: Se debatermos entre simplificar uma mecânica para ficar "sempre resolvível" ou manter o risco real de travar, este pilar diz: mantenha o risco, mesmo que aumente a curva de aprendizado.

### Pillar 2: Progresso Permanente

Toda sessão, por mais curta que seja, deixa um traço visível e permanente na Vila.

*Design test*: Entre uma recompensa consumível (booster que desaparece) e uma que constrói a Vila, este pilar escolhe sempre a que constrói.

### Pillar 3: Fácil de Entender, Difícil de Dominar

Uma criança entende em 5 segundos; um mestre ainda encontra desafio depois de 100 níveis.

*Design test*: Se uma mecânica nova precisa de mais de 10 segundos de tutorial, ela é adiada para depois do nível 20, nunca colocada no início.

### Pillar 4: Monetização por Alegria, não por Fricção

Compramos beleza e coleção — nunca vantagem ou alívio de punição artificial.

*Design test*: Se uma feature de monetização bloqueia o progresso central vs. oferece um atalho cosmético opcional, este pilar escolhe sempre o atalho opcional.

### Pillar 5: Picos de Maestria Calibrados

Desafio de verdade existe, mas é raro e anunciado — não constante.

*Design test*: Se um nível regular "quer" ser mais difícil que o padrão, ele vira um Nó-História — nunca substitui um nível normal.

### Anti-Pillars (What This Game Is NOT)

- **NÃO** teremos sistema de vidas/energia que bloqueia o jogo por horas sem pagamento — isso violaria o Pilar 4 (Monetização por Alegria).
- **NÃO** teremos PvP ou comparação competitiva direta entre jogadores — isso violaria a fantasia central de calma tátil e o público-alvo de Relaxamento & Flow.
- **NÃO** exigiremos reflexo em tempo real (sem cronômetros apertados, sem contagem regressiva de vida) — isso quebraria o Pilar 3.
- **NÃO** teremos narrativa pesada com diálogos longos — a Vila tem personalidade leve, mas o jogo não é narrativo; isso preserva a leveza do loop momento-a-momento.

---

## Visual Identity Anchor

**Direção visual**: Aconchego Tátil

**Regra visual em uma linha**: Tudo parece que dá pra tocar e é feito de lã de verdade — texturas fofas, bordas arredondadas, luz quente.

**Princípios de apoio**:
- **Squash & stretch em tudo que reage** — qualquer elemento que responde a uma ação do jogador tem deformação elástica visível — nada rígido ou mecânico. *Teste de design*: se um elemento reage a uma ação do jogador e não deforma, está errado.
- **Paleta pastel saturada, nunca neon** — cores vibrantes o bastante para diferenciar fios claramente, mas sempre com um tom acolhedor. *Teste de design*: se uma cor parece fria ou agressiva, ela não pertence à paleta.

**Filosofia de cor**: base pastel quente (Vila, fundo, UI) + acentos saturados e claramente distintos por cor de fio (clareza funcional e acessibilidade a daltonismo) — aconchegante mas nunca confuso.

---

## Inspiration and References

| Reference | What We Take From It | What We Do Differently | Why It Matters |
| ---- | ---- | ---- | ---- |
| Wool Crush / Wool Sort 3D | A satisfação ASMR tátil de organizar fios coloridos | Física real de corda com risco de travamento em vez de slots de ordenação automática | Valida que existe apetite grande e comprovado por puzzles táteis de lã |
| Merge Mansion | O loop de coleção/construção persistente que nunca se perde | Meta ligada diretamente à conclusão dos puzzles, não um tabuleiro de merge separado | Valida o alto potencial de retenção e monetização de camadas de coleção |
| Flow Free | A satisfação minimalista do "clique" de conexões corretas | Tensão física real (travamento) em vez de lógica pura sem risco | Valida que formatos minimalistas de puzzle escalam para públicos enormes |

**Non-game inspirations**: artesanato de tricô/crochê, a estética "cozy" (aconchegante), vídeos ASMR de desemaranhar novelos.

---

## Target Player Profile

| Attribute | Detail |
| ---- | ---- |
| **Age range** | 25-45 |
| **Gaming experience** | Casual |
| **Time availability** | Sessões de 5-15 minutos, várias vezes ao dia |
| **Platform preference** | Mobile (iOS / Android) |
| **Current games they play** | Wool Sort 3D, Merge Mansion, Two Dots, Royal Match |
| **What they're looking for** | Um puzzle tátil relaxante com uma sensação diferenciada (não só ordenação automática) e progresso contínuo visível |
| **What would turn them away** | Sistemas de vidas agressivos, pay-to-win, controles confusos, estados de falha punitivos |

---

## Technical Considerations

| Consideration | Assessment |
| ---- | ---- |
| **Recommended Engine** | Godot 4.6 — preferência do desenvolvedor, leve para mobile 2D, exportação gratuita e direta para iOS/Android |
| **Key Technical Challenges** | Simulação de física de corda/fio com detecção justa de travamento; geração procedural de nós com garantia de solubilidade |
| **Art Style** | 2D estilizado com aparência 2.5D (shaders de "felpa" e squash-stretch) |
| **Art Pipeline Complexity** | Medium |
| **Audio Needs** | Moderate — SFX em camadas por interação, música ambiente aconchegante |
| **Networking** | None no MVP (single-player); cloud save pode ser considerado depois |
| **Content Volume** | MVP: ~40-60 nós em 6-8 Bobinas; Visão completa: centenas de nós em múltiplos biomas |
| **Procedural Systems** | Geração procedural de nós com validação algorítmica de solubilidade — sistema técnico central do jogo |

---

## Risks and Open Questions

### Design Risks
- Calibrar a frequência de travamento é delicado — travar fácil demais frustra, travar difícil demais remove a aposta que dá graça ao jogo
- O apelo do Nó-História pode não se sustentar se a curadoria manual não acompanhar o ritmo de conteúdo

### Technical Risks
- Garantir algoritmicamente que todo nó gerado seja sempre solucionável de forma justa é o maior risco técnico do projeto
- Física de corda/fio pode ter custo de performance em aparelhos mobile de entrada — precisa de perfil de performance cedo

### Market Risks
- O gênero wool-sort é extremamente saturado no Google Play — o diferencial de física precisa ficar óbvio no marketing (screenshots, vídeo de loja), senão o jogo se perde entre clones

### Scope Risks
- A camada dupla (puzzle + meta de coleção) exige balancear duas economias de recompensa simultaneamente — risco de escopo para um desenvolvedor solo em primeiro jogo

### Open Questions
- O algoritmo de geração de nós garante 100% de solubilidade justa? — precisa de protótipo técnico dedicado antes de qualquer outro trabalho
- Qual a frequência ideal de travamento para maximizar tensão sem frustrar? — precisa de playtest cedo, mesmo com protótipo simples

---

## MVP Definition

**Core hypothesis**: Jogadores acham o loop de desemaranhar-com-risco-de-travar satisfatório e voltam por causa do crescimento visível da Vila, mesmo em sessões curtas de 5 minutos.

**Required for MVP**:
1. Sistema de física de corda com detecção de travamento e desfazer instantâneo
2. Combo por ordem de resolução
3. Vila com progresso visual permanente (1 bioma, ~10-15 construções/criaturas)
4. ~40-60 nós distribuídos em 6-8 Bobinas

**Explicitly NOT in MVP** (defer to later):
- Nós-História curados à mão (usar gerados proceduralmente no lançamento, curar depois)
- Conteúdo sazonal/eventos de live-ops
- Customização cosmética ampla da Vila (começar com opções mínimas)
- Multiplayer assíncrono/social (visitar vilas de amigos)

### Scope Tiers (if budget/time shrinks)

| Tier | Content | Features | Timeline |
| ---- | ---- | ---- | ---- |
| **MVP** | 1 bioma, ~40-60 nós em 6-8 Bobinas | Física de corda, combo, Vila básica, monetização mínima (ads + 1 IAP) | 8-12 semanas |
| **Vertical Slice** | 1 bioma completo e polido | MVP + alguns Nós-História curados, loop de monetização testado | +4-6 semanas (~14-18 sem. total) |
| **Alpha** | Múltiplos biomas, arte placeholder nos últimos | Todas as features principais presentes, ainda não polidas | +8-10 semanas (~6-7 meses total) |
| **Full Vision** | Múltiplos biomas polidos, conteúdo sazonal | Cadência completa de live-ops, monetização cosmética ampla | 9-14 meses até lançamento full-featured; live-ops contínuo depois |

---

## Next Steps

- [ ] Get concept approval from creative-director
- [ ] Fill in CLAUDE.md technology stack based on engine choice (`/setup-engine`)
- [ ] Create game pillars document (`/design-review` to validate)
- [ ] **Prototype core idea** (`/prototype [core-mechanic]`) — before writing GDDs, validate the concept is worth designing
- [ ] If prototype PROCEEDS: Decompose concept into systems (`/map-systems`)
- [ ] Design each system (`/design-system [system-name]`) — use prototype learnings in Tuning Knobs and Formulas sections
- [ ] Build vertical slice in Pre-Production (`/vertical-slice`) — validate full game loop before committing to Production
- [ ] Validate core loop with playtest (`/playtest-report`)
- [ ] Plan first milestone (`/sprint-plan new`)
