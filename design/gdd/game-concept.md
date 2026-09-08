# Game Concept: Nó Vivo

*Created: 2026-09-01*
*Status: Revisado após `/design-review` de 2026-09-08 — ver `design/gdd/reviews/game-concept-review-log.md`*

> **Nota de forma**: este é um **documento de conceito**, não um GDD de sistema. A regra
> das 8 seções obrigatórias (`.claude/rules/design-docs.md`, `design/CLAUDE.md`) vale para
> os GDDs de sistema que sairão de `/design-system` — Overview, Detailed Rules, Formulas,
> Edge Cases, Dependencies, Tuning Knobs, Acceptance Criteria por sistema. Aplicá-la aqui
> foi avaliado na revisão e descartado explicitamente pelo creative-director: reestruturar
> o conceito para satisfazer uma regra escrita para especificações de sistema destruiria a
> função dele. Os valores de tuning e critérios de aceitação abaixo são **sementes** para
> aqueles GDDs, não substitutos.

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
| **Monetization** | F2P — rewarded opt-in (dobrar a Lã de uma Bobina) + SKU único "remover anúncios". Lã **não** é comprável. Sem ad de "continuar" (não existe estado de falha do qual continuar). |
| **Estimated Scope** | **~8-10 meses part-time até publicar** (dev solo, primeiro jogo, multiplicador de primeira-vez aplicado). Postura: **aprender primeiro** — receita é secundária, terminar e publicar é o objetivo. Live-ops e multi-bioma ficam fora do v1.0. |
| **Comparable Titles** | Wool Crush / Wool Sort 3D, Merge Mansion, Flow Free |

---

## Core Fantasy

A satisfação tátil de desfazer o caos com as próprias mãos — sentir a lã **resistir, endurecer e ceder** sob o dedo, como se o nó respondesse — combinada com o orgulho tranquilo de ver algo bonito e permanente crescer a partir desse esforço. Não é sobre pressão ou competição: é sobre a certeza calma de "eu consigo resolver isso", seguida da alegria de ver a Vila florescer com cada vitória.

**O que esta fantasia NÃO promete** (corrigido na revisão de 2026-09-08): não é a fantasia de resolver um quebra-cabeça lógico profundo. Todo nó é resolvível olhando quais pontas não têm nada por cima — é **leitura visual**, não planejamento de várias jogadas à frente. Ler bem é recompensado (o combo multiplica a Lã, e a Vila cresce mais rápido), mas ler bem nunca é *obrigatório* para terminar. A promessa é sensorial primeiro, de eficiência depois.

---

## Unique Hook

**Lã que puxa de volta.**

É como um jogo de wool-sort (Wool Crush, Wool Sort 3D), E TAMBÉM os fios têm física de corda simulada de verdade — o fio resiste ao dedo, estica, afina, e **cede com um estalo** quando está livre; quando está preso, ele bate numa parede sólida e o nó inteiro se aperta em volta enquanto você força. Os concorrentes do gênero respondem a um toque; este responde a uma **tração**.

O diferencial é sensorial e afeta a jogabilidade central, não é verniz visual — mas ele é de **sensação**, não de complexidade combinatória. O protótipo mediu a assimetria que sustenta isso: um fio livre cede após ~64px de dedo; um fio preso trava em 62px de esticamento e não sai nem com 240px de arrasto.

*Consequência para marketing:* isso é excelente material de vídeo — é visível em 3 segundos de gravação de tela, que é como este gênero é adquirido. É por aí que o jogo se separa dos clones, não por profundidade de puzzle.

---

## Player Experience Analysis (MDA Framework)

### Target Aesthetics (What the player FEELS)

| Aesthetic | Priority | How We Deliver It |
| ---- | ---- | ---- |
| **Sensation** (sensory pleasure) | 1 | Física elástica dos fios, áudio em camadas (boing, acorde ascendente, estalo), partículas de lã |
| **Fantasy** (make-believe, role-playing) | N/A | — |
| **Narrative** (drama, story arc) | N/A | — |
| **Challenge** (obstacle course, mastery) | 3 | Combo (a aposta real: desfazer quebra a sequência), Nós-História cadenciados. **Nota**: esta prioridade 3 é a correta — a revisão de 2026-09-08 confirmou que o jogo é Sensation-primário, não um jogo de desafio. |
| **Fellowship** (social connection) | N/A | Fora do MVP — possível expansão futura (visitar vilas de amigos) |
| **Discovery** (exploration, secrets) | 4 | Desbloqueio progressivo de construções e criaturas na Vila |
| **Expression** (self-expression, creativity) | 5 | Customização cosmética da Vila |
| **Submission** (relaxation, comfort zone) | 2 | Paleta quente, ausência de pressão de tempo, sem punição severa |

### Key Dynamics (Emergent player behaviors)

- Jogadores vão desenvolver **leitura visual rápida** — bater o olho e identificar quais pontas estão livres antes de puxar, em vez de tocar aleatoriamente. O que empurra esse comportamento é o combo, não a necessidade: chutar também resolve o nó, só rende menos Lã.
- Jogadores vão voltar para checar o progresso visual da Vila mesmo em sessões onde só querem "dar uma olhada"
- Jogadores mais engajados vão buscar ativamente os Nós-História pelo desafio, enquanto o público casual pode ignorá-los sem prejuízo

### Core Mechanics (Systems we build)

**Sistema primário** (o diferencial do jogo; validado por protótipo). A revisão de
2026-09-08 separou o que antes era um item só, porque são três sistemas com donos e
superfícies de tuning diferentes — misturá-los faz alguém tentar ajustar frequência
de travamento mexendo em constante de mola:

1. **Regras topológicas de travamento** — quem está por cima de quem, o que está
   destravável, geração dos cruzamentos. Discreto. É aqui que a dificuldade mora.
2. **Simulação de tensão do fio** — cadeia de Verlet, resistência, parede de
   esticamento, deformação, oscilação. Contínuo. É aqui que a *sensação* mora.
3. **Desfazer e estado** — snapshot, profundidade de undo, restauração do aperto.

**Sistemas de suporte:**

4. Sistema de combo por ordem de resolução — **é a única aposta do jogo** (ver
   "A aposta" abaixo); multiplica a Lã e, portanto, o ritmo da Vila
5. Geração de nós com solubilidade garantida por construção — **executada offline**,
   nós enviados como dados (decisão de escopo: sem geração em runtime no v1.0)
6. Meta de coleção/construção permanente — Vila de Lã alimentada por Lã coletada
7. Nós-História — picos de dificuldade curados, cadenciados a cada ~10 níveis

---

## A aposta (o que ler bem dá ao jogador)

**Decisão de 2026-09-08.** Sem isto, quem lê o nó perfeitamente e quem chuta e
desfaz cinco vezes terminam com exatamente a mesma Lã e a mesma Vila — e o Pilar 1
fica sem consequência nenhuma.

- **Desfazer continua grátis e instantâneo.** Nenhuma vida perdida, nenhuma Bobina
  perdida, nenhum tempo de espera. O anti-pilar de punição está intacto.
- **Mas desfazer quebra o combo.** E o combo multiplica a Lã ganha na Bobina.
- Logo: **quem lê bem progride visivelmente mais rápido; quem chuta ainda termina.**

Isto é o que liga causalmente as duas metades da Core Fantasy (competência → orgulho):
a Vila cresce mais rápido para quem joga melhor, em vez de crescer igual para todos.

> **Precisa de fórmula antes do GDD.** `combo_multiplicador = f(puxadas_ótimas_consecutivas)`
> e a regra exata de reset ao travar. Sem número, isto não é implementável nem
> balanceável — e como é a única fonte de aposta do jogo, é o item mais carregado do
> projeto.

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

- **Onboarding curve**: o primeiro nó **pode travar de verdade** — e é exatamente isso que ele ensina. (A versão anterior dizia "2 fios, impossível de travar", o que é geometricamente impossível: dois fios que se cruzam significam que um está por baixo, logo preso.) Sequência dos primeiros 60 segundos: (1) abre direto no nó 1, sem menu; (2) um dedo-fantasma animado demonstra a puxada correta; (3) o jogador repete e sente o fio ceder; (4) apresenta-se um fio preso — se ele puxar, **trava de verdade**, mas sem custo nenhum e com o Desfazer aparecendo ao lado; (5) o nó 2 já vale combo. Gesto → regra → consequência, sem mentir sobre a mecânica.
- **Difficulty scaling**: fios aumentam gradualmente em número e cruzamento; mecânicas novas (cordas travadas/congeladas) só entram depois do nível 20
- **Feedback clarity**: menos usos de "desfazer" e combos mais longos são indicadores visíveis e imediatos de habilidade crescente
- **Recovery from failure**: desfazer é instantâneo e **não custa progresso** — nunca perde a Bobina, nunca perde Lã já ganha, nunca espera. **Custa o combo**, e só isso. (Antes da revisão o documento dizia "sem custo" num lugar e implicava custo de combo noutro; a regra acima é a única versão válida.)
- **O que torna a falha educativa** (mecanismo nomeado, não adjetivo): ao forçar um fio preso, **o nó inteiro se aperta visivelmente em volta dele** e o fio que o está prendendo é destacado. O jogador vê o erro se formando *antes* de soltar. Esse é o ensino — não é um texto de tutorial.

---

## Core Loop

### Moment-to-Moment (30 seconds)

Puxar pontas de fios emaranhados com física elástica real. Escolher a ordem errada pode travar o nó (exigindo desfazer); a ordem certa libera o fio com feedback sonoro e visual satisfatório (boing, acorde ascendente, partículas de lã ao limpar uma cor).

### Short-Term (5-15 minutes)

Um "novelo" (nó individual) leva 10-20 segundos; vários novelos formam uma "Bobina" (nível, 3-8 mini-puzzles). Completar uma Bobina rende Lã **multiplicada pelo combo**, que constrói visivelmente um prédio na Vila — o gancho de "só mais um pouco para terminar a casa" puxa a próxima rodada. Como o combo escala a Lã, **jogar melhor faz a Vila crescer mais rápido**: é aqui que a competência vira orgulho visível, em vez de as duas coisas apenas acontecerem em sequência.

### Session-Level (30-120 minutes)

Várias Bobinas completadas resultam em um marco visual na Vila. A cada ~10 níveis surge um Nó-História — puzzle curado, propositalmente difícil. Parada natural ao fim de cada Bobina; o retorno é motivado pela construção inacabada da Vila e por um "nó especial do dia".

### Long-Term Progression

O jogador cresce em dois eixos no v1.0: a Vila (coleção visual permanente, construída com Lã) e a leitura visual (nós ficam mais densos, e o combo recompensa quem lê antes de puxar).

**Correção de escopo (2026-09-08)**: a versão anterior descrevia um jogo-serviço multi-anos com biomas sazonais. Isso saiu do v1.0 sob a postura "aprender primeiro" — o objetivo é **terminar e publicar**. Multi-bioma e sazonal voltam à mesa só se a retenção medida justificar. O v1.0 tem fim: a Vila do primeiro bioma fica completa.

### Retention Hooks

- **Curiosity**: qual construção desbloqueia a seguir na Vila
- **Investment**: a Vila que já construíram — vínculo emocional com o progresso visível
- **Social**: fora do v1.0 inteiro (não só do MVP). *Nota do economy-designer*: economias cosméticas monetizam porque as compras são **vistas**; sem camada social, cosmético converte mal — foi por isso que a monetização virou rewarded + remover-ads em vez de catálogo cosmético.
- **Mastery**: combo (o multiplicador de Lã torna a maestria visível no ritmo da Vila), redução de "desfazer" ao longo do tempo, Nós-História
  - ⚠️ **Nós-História estão fora do MVP**, e são o veículo nomeado do Pilar 3 ("difícil de dominar") e de todo o Pilar 5. Isso significa que **um pilar e meio seguem como hipótese não validada** até bem depois do MVP. Registrado, não resolvido.

---

## Game Pillars

### Pillar 1: Tensão Tátil Real

Cada nó **responde fisicamente** — o fio livre resiste e cede, o fio preso endurece e o nó se aperta em volta. A ordem errada trava; a ordem certa flui.

*Design test*: Se debatermos entre simplificar uma mecânica para ficar "sempre resolvível" ou manter o risco real de travar, este pilar diz: mantenha o risco, mesmo que aumente a curva de aprendizado.

*Escopo do risco* (esclarecido em 2026-09-08): o risco é de **eficiência**, não de progresso. Travar nunca tira nada do jogador — quebra o combo. Todo nó continua sendo sempre resolvível por construção.

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
  - **Exceção obrigatória (2026-09-08): o fio preso na parede de tensão NÃO deforma.** A descoberta central do protótipo v2 foi que um fio travado precisa parecer que bateu em algo *sólido* — foi isso que transformou "pouco tátil" em "muito melhor". Aplicar squash ali destruiria a única leitura que diferencia "vai ceder" de "não vai sair". A rigidez naquele momento é intencional e vence este princípio.
  - **Exceção obrigatória: os pontos de cruzamento não deformam.** Deformar exatamente a geometria que codifica quem está por cima, enquanto o jogador tenta lê-la, é contraproducente. Squash & stretch aplica-se aos vãos livres entre cruzamentos e à ponta arrastada.
- **Paleta pastel saturada, nunca neon** — cores vibrantes o bastante para diferenciar fios claramente, mas sempre com um tom acolhedor. *Teste de design*: se uma cor parece fria ou agressiva, ela não pertence à paleta.

**Filosofia de cor**: base pastel quente (Vila, fundo, UI) + acentos saturados e claramente distintos por cor de fio — aconchegante mas nunca confuso.

**Acessibilidade — correção de 2026-09-08.** A versão anterior alegava acessibilidade a daltonismo "via cores saturadas distintas". Isso é falso: o eixo que falha no daltonismo é **matiz**, não saturação — protanopia e deuteranopia colapsam vermelho-verde por mais saturados que sejam. Além disso o padrão do projeto exige "funcional sem depender de cor". Requisitos obrigatórios:

- **Diferenciador não-cromático por fio**: padrão de torção ou de tracejado distinto, embutido no shader de lã por slot de cor.
- **Sombra/AO consistente no fio de baixo em todo cruzamento** — independente de matiz. Isto também serve de leitura over/under quando a máscara de fundo chapado for substituída por z-ordering real.
- Validar a paleta final em simulador de daltonismo (protanopia / deuteranopia / tritanopia) **testando cruzamentos, não amostras de cor isoladas**, e arquivar a captura em `production/qa/evidence/`.

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
| **Recommended Engine** | Godot 4.6 — preferência do desenvolvedor, leve para mobile 2D, exportação gratuita e direta para iOS/Android. **Linguagem**: GDScript + C# desde o início (decisão consciente do dev, tomada em 2026-09-08 contra a recomendação do godot-specialist, que apontou que a simulação — ~260 pontos, 12 passadas/quadro — não exige C# e que o custo é toolchain .NET, binário maior e depuração cross-language no primeiro jogo). Registrar como ADR. Se C# for usado na simulação, **todo o laço por fio** vive em C#: uma fronteira cruzada por ponto por quadro custa mais em marshaling de Variant do que a matemática economiza. |
| **Key Technical Challenges** | **A física de corda não é um desafio de implementação — é o sistema primário do jogo** (ver Core Mechanics). Riscos técnicos restantes: renderizar over/under sobre fundo não-chapado; orçamento de draw calls com shader de felpa por fio; verificar hápticos em iOS. |
| **Art Style** | 2D estilizado com aparência 2.5D (shaders de "felpa" e squash-stretch) |
| **Art Pipeline Complexity** | Medium |
| **Audio Needs** | Moderate — SFX em camadas por interação, música ambiente aconchegante |
| **Networking** | None no MVP (single-player); cloud save pode ser considerado depois |
| **Content Volume** | MVP: ~40-60 nós em 6-8 Bobinas; Visão completa: centenas de nós em múltiplos biomas |
| **Procedural Systems** | Gerador **offline**: roda em ferramenta de dev, nós enviados como dados no v1.0. Custo zero em runtime, sem engasgo entre novelos, e permite conferir a qualidade de cada nó à mão — que é o único jeito de calibrar frequência de travamento hoje. Geração em runtime volta pós-lançamento se a retenção justificar. |
| **Renderização over/under** | O truque do protótipo (halo da cor do fundo) **só funciona com fundo chapado** e vai quebrar contra o cenário da Vila. Usar o z-ordering real derivado da ordem de camadas que a geração já produz. Um shader de felpa que estende geometria além da silhueta vaza através do cruzamento mesmo com z correto — a estratégia de máscara precisa ser decidida **antes** de escrever o shader. |
| **Orçamento de draw calls** | Material de shader por fio quebra o batching 2D do Godot. Combinado com passe de máscara e overlay de fibras, um único nó pode consumir fatia relevante dos ~100 draw calls antes de Vila, UI e partículas entrarem. Precisa de plano de batching antes da implementação. Partículas de lã: `CPUParticles2D`, não GPU. |

---

## Risks and Open Questions

### Design Risks
- Calibrar a frequência de travamento é delicado — travar fácil demais frustra, travar difícil demais remove a aposta que dá graça ao jogo
- O apelo do Nó-História pode não se sustentar se a curadoria manual não acompanhar o ritmo de conteúdo

### Technical Risks
- ~~Garantir solubilidade é o maior risco técnico~~ — **RESOLVIDO (protótipo, 2026-09-08)**: os fios são empilhados em ordem total de camadas, então sempre existe um fio no topo sem nada por cima. Solubilidade garantida por construção, sem validação por busca.
  - **Limite da garantia**: ela **não cobre** as cordas travadas/congeladas planejadas para depois do nível 20. Um fio que não sai independentemente da camada não está coberto pela prova. Esse risco continua **aberto** para aquele conteúdo e precisa de argumento próprio antes de ser construído.
- Física de corda pode ter custo de performance em aparelhos de entrada — precisa de profile cedo em aparelho real, não em navegador
- Renderização over/under sobre fundo texturizado e orçamento de draw calls (ver Technical Considerations)
- Hápticos afirmados mas não verificados; o suporte do Godot em iOS é historicamente mais limitado que em Android

### Market Risks
- O gênero wool-sort é extremamente saturado no Google Play — o diferencial de física precisa ficar óbvio no marketing (screenshots, vídeo de loja), senão o jogo se perde entre clones

### Scope Risks
- A camada dupla (puzzle + meta de coleção) exige balancear duas economias simultaneamente — risco de escopo para dev solo em primeiro jogo.
  - **Status: risco aceito conscientemente em 2026-09-08.** O creative-director recomendou cortar a economia de construção e manter só "cena única que cresce" (entregaria o Pilar 2 inteiro por ~20% do escopo). O desenvolvedor optou por manter a economia completa. Consequência registrada: o prazo realista sai de 4-6 meses para **~8-10 meses part-time**, e a esteira de arte do catálogo passa a ser custo recorrente.
- **Ergonomia limita a alavanca de dificuldade**: 4 fios dão ~187px de separação entre pontas; 8 fios caem para ~93px, que depois da redução para tela de celular fica igual ou abaixo do mínimo de 44pt de alvo de toque. **Contagem de fios acaba nos dedos antes de acabar no design** — e é eixo de carga perceptual, não de profundidade. Precisa de uma segunda alavanca de dificuldade.

### Open Questions

**FECHADA** — *O algoritmo garante 100% de solubilidade?* Sim, por construção (ordem
total de camadas). Não cobre cordas travadas/congeladas pós-nível-20.

**ABERTA — e é a mais importante do projeto.** *Qual a frequência ideal de travamento?*
A pergunta original misturava duas coisas e foi fechada por engano junto com a
solubilidade. São distintas: "todo nó tem solução?" (fechada, verificável em código) e
**"a frequência de travamento parece justa em vez de arbitrária?"** (aberta, nunca
medida, sem sequer um número alvo provisório). Sem um alvo, ninguém consegue dizer se
um lote de nós gerados está calibrado ou não.

**ABERTA — bloqueante.** *A mecânica é legível para quem não a projetou, no celular?*
Os dois playtests do protótipo tiveram um único testador — o próprio autor da mecânica —
e a observação de legibilidade foi colhida no v1, cujos cruzamentos eram pintados e não
existem mais. Todos os números de sensação vieram de mouse, que não tem oclusão; um
polegar cobre 40-70px contra um limiar de 64px. Fecha com 2-3 pessoas externas, ≥1 no
celular, no build v2 que já existe. Ver a emenda em
`prototypes/no-fisica-corda-concept/REPORT.md`.

**ABERTA.** *Qual a fórmula do combo?* É a única aposta do jogo e não tem número.

---

## MVP Definition

**Core hypothesis** (reescrita em 2026-09-08 — a versão anterior era infalsificável:
"satisfatório" não tem definição operacional e "voltam **por causa de** X" afirma uma
causalidade que nenhum evento de analytics consegue atribuir):

| # | Hipótese | Como medir | Limiar |
| --- | --- | --- | --- |
| **H1** | O loop engaja mesmo quando falha | entre jogadores que travam um nó, % que retenta em vez de abandonar (`knot_locked` → `knot_retry` vs `level_abandon`), primeiras 3 sessões | ≥ 60% |
| **H2** | A habilidade cresce de verdade | média de "desfazer" por nó, em nós de mesma contagem de fios, ao longo das 5 primeiras exposições | tendência de queda |
| **H3** | O tamanho de sessão bate com o design | mediana de duração de sessão | 4-16 min |
| **H4** | O jogo retém | D1 / D7 | ≥30% / ≥10% ⚠️ |

⚠️ **Os números de H4 e H3 são PLACEHOLDER SEM FONTE.** Nenhum benchmark foi citado em
lugar nenhum deste documento. Precisam de fonte real antes de virarem portão no
`/gate-check` — um número inventado que vira gate é pior que nenhum número.

**Regra de falsificação**: se H1 < 40% **e** D1 < 15%, a hipótese é rejeitada
independentemente de elogio qualitativo em playtest. "Ficou muito melhor" não é
evidência a favor nem contra.

**Required for MVP**:
1. Simulação de tensão do fio + regras topológicas de travamento + desfazer (três sistemas, ver Core Mechanics)
2. Combo por ordem de resolução — **com fórmula definida**; é a única aposta do jogo
3. Vila com economia de construção e progresso permanente (1 bioma, ~10-15 construções/criaturas)
4. ~40-60 nós gerados **offline** e enviados como dados, em 6-8 Bobinas
5. Monetização: rewarded opt-in (dobrar Lã de uma Bobina) + SKU "remover anúncios"

**Explicitly NOT in MVP** (defer to later):
- Nós-História curados à mão (usar gerados proceduralmente no lançamento, curar depois)
- Conteúdo sazonal/eventos de live-ops
- Customização cosmética ampla da Vila (começar com opções mínimas)
- Multiplayer assíncrono/social (visitar vilas de amigos)

### Critérios de Sucesso Mensuráveis

*Adicionados em 2026-09-08 — o documento não tinha nenhum. Todo item aqui precisa ser
verificável por outra pessoa, não por opinião do autor.*

| Área | Critério | Tipo de evidência |
| --- | --- | --- |
| Solubilidade | 100% dos nós enviados passam em verificação automatizada de solubilidade | Teste unitário, bloqueante em CI |
| Legibilidade da regra | ≥ 80% de testadores não avisados explicam corretamente por que uma puxada travou (amostra ≥5) | Playtest moderado, gravação em `production/qa/evidence/` |
| Calibragem de travamento | % alvo de puxadas que travam, por faixa de dificuldade — **número a definir** | Telemetria + lote de nós |
| Combo | Fórmula produz multiplicador dentro da faixa esperada nos valores de fronteira | Teste unitário, bloqueante |
| Economia da Vila | Curva de custo não ultrapassa a renda de Lã em nenhum ponto (senão vira portão de energia disfarçado) | Planilha + teste de integração |
| Pitch | ≥4 de 5 pessoas não avisadas repetem as duas ações (puxar fios na ordem, construir vila) em 10s | Teste presencial |
| Acessibilidade | Cruzamentos legíveis em simulador de daltonismo nos três tipos | Captura arquivada |
| Sensação | *Não automatizável* por padrão do projeto — captura + assinatura do lead | Advisory |

> **Lacuna de processo identificada na revisão**: a hipótese de retenção não se encaixa
> em nenhuma das cinco categorias de evidência do projeto (Logic / Integration /
> Visual / UI / Config). Ela só é verificável com telemetria real pós-soft-launch.
> O projeto precisa de uma categoria **"Playtest/Comportamental"** antes que o
> `/gate-check` possa aprovar ida para Produção.

### Scope Tiers

*Revisados em 2026-09-08 com multiplicador de primeiro projeto aplicado. As estimativas
anteriores (MVP em 8-12 semanas) não sobreviveram à revisão: estimativas de primeiro
projeto neste setor erram por 2-4×.*

| Tier | Content | Features | Timeline realista |
| ---- | ---- | ---- | ---- |
| **MVP jogável** | 1 bioma, ~40-60 nós offline em 6-8 Bobinas | 3 sistemas de corda, combo com fórmula, Vila com economia, ads + remover-ads | ~5-6 meses part-time |
| **Publicável (v1.0)** | MVP polido, onboarding, loja | + alguns Nós-História curados, telemetria, submissão nas lojas | ~8-10 meses part-time |
| **Pós-lançamento** | Multi-bioma, sazonal, geração em runtime | Live-ops, catálogo cosmético ampliado, social opcional | Só se a retenção justificar |

**Fora do v1.0, explicitamente**: multi-bioma, conteúdo sazonal, live-ops, catálogo
cosmético amplo, geração procedural em runtime, qualquer camada social.

---

## Valores de Tuning Descobertos (sementes para os GDDs)

*Do protótipo v2. **Atenção**: todos foram medidos em canvas de 900×600 numa janela de
navegador desktop, com mouse. São unidades de canvas, não device-independent — o mesmo
"46px" corresponde a um deslocamento físico de polegar completamente diferente em um
celular. **Reexpressar como razão do raio do nó ou da largura de viewport antes de entrar
em qualquer GDD**, e revalidar em aparelho real.*

| Parâmetro | Valor (canvas 900×600, mouse) | O que controla |
| --- | --- | --- |
| Ceder do fio livre | 46px de esticamento (~64px de dedo) | Quando um fio destravado escapa |
| Parede absoluta | 62px de esticamento | Onde um fio preso para de sair |
| Travar ao soltar | acima de 34px de esticamento | Limiar que cinde o nó |
| Pontos por fio | 26 | Resolução da simulação |
| Iterações | 6 relaxações × 2 substeps | Estabilidade das restrições |
| Amortecimento | 0.90 | Lã não quica |
| Mola da ponta | 0.34 | Defasagem dedo↔ponta = leitura de resistência |
| Aperto do nó | 16px no fio que prende, ~45% nos vizinhos | Feedback antecipado de erro |
| Teto ergonômico de fios | ~4-6 (8 fios → ~93px entre pontas, abaixo do mínimo de toque) | Limite superior da contagem de fios |

---

## Decisões Travadas (2026-09-08)

Tomadas pelo desenvolvedor após o `/design-review`. Registradas aqui para não serem
relitigadas — e, onde contrariam a recomendação, com a recomendação preservada.

| # | Decisão | Escolha | Observação |
| --- | --- | --- | --- |
| **D1** | Postura de negócio | **Aprender primeiro** | Receita é secundária; terminar e publicar é o objetivo. Resolve a contradição escalada por economy-designer e game-designer: o objetivo original de "dinheiro rápido" foi conscientemente trocado por "primeiro jogo publicado". |
| **D2** | Fonte de profundidade | **Combo é a aposta + assumir o enquadramento sensorial** | O jogo é Sensation-primário. Desfazer é grátis mas quebra o combo, que multiplica a Lã. |
| **D3** | Escopo da Vila | **Economia de construção completa** | ⚠️ *Contra a recomendação.* O creative-director recomendou cena única (Pilar 2 inteiro por ~20% do escopo). Custo aceito: prazo de 4-6 → ~8-10 meses, mais esteira de arte recorrente. |
| **D4** | Linguagem | **GDScript + C# desde o início** | ⚠️ *Contra a recomendação.* O godot-specialist mostrou que a simulação não exige C#. Aceito como objetivo de aprendizado do dev. Registrar ADR. |

---

## Next Steps

- [x] Configurar engine (`/setup-engine`) — Godot 4.6
- [x] **Prototipar a ideia central** (`/prototype`) — PROCEED WITH CONDITION
- [x] Revisar o conceito (`/design-review`) — MAJOR REVISION NEEDED, aplicado neste documento
- [ ] **BLOQUEANTE: playtest externo do v2** — 2-3 pessoas não avisadas, ≥1 no celular. Fecha a condição do protótipo e a lacuna do polegar de uma vez só.
- [ ] Registrar ADR da decisão de linguagem (`/architecture-decision`)
- [ ] `/gate-check` — sair da fase de Conceito
- [ ] `/map-systems` — decompor em sistemas (os 3 de corda entram separados)
- [ ] `/design-system` por sistema — usar os Valores de Tuning acima, **convertidos para razões**
- [ ] `/vertical-slice` antes de comprometer com Produção
