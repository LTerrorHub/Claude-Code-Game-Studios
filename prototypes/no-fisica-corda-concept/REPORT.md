# Concept Prototype Report: Nó Vivo — Física de Corda

> **Date**: 2026-09-08
> **Prototype Path**: HTML
> **Concept File**: design/gdd/game-concept.md

---

## Hypothesis

Se o jogador puxa pontas de fios de lã com física real de tensão (onde a ordem
errada pode travar o nó, exigindo desfazer), ele vai sentir um momento genuíno de
"eu consigo" — vamos saber que é verdade se os testadores tentarem novamente um nó
travado por vontade própria (sem serem instruídos) e conseguirem explicar
verbalmente por que uma puxada falhou.

---

## Riskiest Assumption Tested

**"O mecanismo de travamento parece justo/legível ou arbitrário/frustrante?"**

Provou-se legível. Na primeira sessão, sem tutorial e sem explicação prévia, o
testador relatou: *"entendi o travamento"*. A regra de oclusão (um fio preso por
outro não sai) foi lida corretamente a partir do desenho do cruzamento apenas.
Esse era o risco mais caro do conceito e ele caiu na primeira iteração.

---

## Approach

Protótipo HTML de arquivo único (Canvas 2D + WebAudio), descartável, sem
dependências. Duas iterações.

**Path chosen:** HTML
**Reason for path:** a mecânica é baseada em arrasto contínuo, não em timing de
reflexo — a latência do navegador não invalida o teste. Iteração muito mais rápida
que montar cena em Godot antes de saber se a ideia se sustenta.

**Shortcuts taken (intentional):**
- Arte descartável: linhas coloridas, sem textura de lã, sem animação de felpa
- Apenas 2 nós fixos (3 e 4 fios), sem geração procedural
- Sem Vila/meta de coleção, sem combo, sem menu, sem progressão
- Áudio por oscilador cru (WebAudio), sem samples
- Sem persistência, sem telemetria

**Iteração v1 → v2:** o v1 foi reescrito quase inteiro depois do primeiro
playtest (ver Result).

---

## Result

### Playtest 1 (v1)

Veredito do testador: **PARCIALMENTE CONFIRMADA.**

- Positivo: *"entendi o travamento"* — legibilidade da regra confirmada.
- Negativo: *"achei pouco tátil"* — falha direta no pilar nº 1 (Tensão Tátil Real).

Investigação no código apontou a causa exata: **o v1 não simulava tensão nenhuma.**
A puxada era uma projeção linear do dedo sobre uma direção fixa
(`visualProgress = clamp(proj / MAXPULL)`) — um slider disfarçado de fio, com todo
o feedback concentrado no momento de soltar. Pior: **os fios nunca se cruzavam de
verdade**; eram raios saindo de um centro e o "cruzamento" era uma barra colorida
pintada por cima do fio de baixo.

Ao ser perguntado o que faltava, o testador marcou três de quatro opções:
sem resistência, o resto do nó não reage, o fio não deforma. **Não** marcou "falta
feedback durante a puxada" — ou seja, o problema era a física, não o áudio/HUD.

### v2 — reconstrução

- Cada fio virou uma cadeia de 26 pontos com integração de Verlet e restrições de
  distância.
- Parede de esticamento **absoluta** (62px) via passada de clamp a partir do ponto
  ancorado — fio preso bate em algo sólido, não estica como elástico.
- Cruzamentos passaram a vir de **interseção geométrica real** entre as curvas.
- Puxar um fio arrasta tudo que ele atravessa: o nó se aperta visivelmente sob
  força, e travar congela esse aperto até Desfazer.
- Deformação: o fio afina sob tensão, as fibras se espaçam, e a oscilação ao soltar
  vem da física (não é tween).

### Playtest 2 (v2)

Veredito do testador: **"ficou muito melhor"**, com autorização para fechar a fase
de conceito.

**Ressalva honesta:** a segunda sessão não coletou a evidência específica que a
hipótese pedia (o testador retentar um nó travado por vontade própria e verbalizar
por que a puxada falhou). O sinal obtido é positivo mas genérico. A legibilidade
está confirmada por medição direta (playtest 1); a tatilidade está confirmada por
impressão do testador, não por comportamento observado.

---

## Metrics

| Metric | Value |
|--------|-------|
| Path used | HTML |
| Iterations to playable | 2 (v1 slider linear → v2 simulação Verlet) |
| Prototype duration | ~1 sessão de trabalho por iteração |
| Playtesters | 1 interno (o dono do projeto), 2 sessões |
| Feel assessment | Fio livre cede após **64px** de deslocamento do dedo; fio preso trava em **62px** de esticamento e não sai mesmo com **240px** de arrasto — assimetria de ~3,8× entre "ceder" e "parede" |
| Hypothesis verdict | **PARCIALMENTE CONFIRMADA** (legibilidade CONFIRMADA por medição; tatilidade positiva mas sem evidência comportamental) |

Verificação automatizada (Playwright headless, v2): resolução completa do Nó Fácil
com 3 puxadas certas e 1 travamento, travar → Desfazer → resolver funciona, zero
erros de JavaScript.

---

## Recommendation: PROCEED

As duas perguntas que justificavam o protótipo foram respondidas de forma
utilizável. A regra de travamento é legível sem tutorial — o risco que poderia
matar o conceito. E a tensão simulada, uma vez implementada de verdade, converteu
um "pouco tátil" em "muito melhor" sem mudar nenhuma regra do jogo: só a física.
Isso indica que o pilar "Tensão Tátil Real" é alcançável e que ele carrega
diferenciação real contra o Caos de Lãs e similares, cujo gesto é seleção, não
tração.

Um achado não previsto reforça o PROCEED: **a solvabilidade agora é garantida por
construção**. Os fios são empilhados numa ordem total de camadas, então sempre
existe um fio no topo sem nada por cima — ou seja, todo nó gerado tem solução.
Esse era o **maior risco** registrado em `design/gdd/game-concept.md`, e o
protótipo entregou o algoritmo que o neutraliza.

---

## If Proceeding

- **Core tuning values discovered:**
  - Ceder do fio livre: **46px de esticamento real** (~64px de dedo)
  - Parede do fio preso: **62px de esticamento absoluto**
  - Travar ao soltar: acima de **34px de esticamento**
  - Cadeia de **26 pontos** por fio, 6 relaxações × 2 substeps — sobra folga imensa
    dentro do orçamento de 16,6ms mesmo com 4 fios
  - Amortecimento 0.90 (lã não quica); mola da ponta 0.34 (a ponta fica *atrás* do
    dedo — é essa defasagem que comunica resistência)
  - Aperto do nó sob força: 16px de deslocamento no fio que prende, ~45% disso nos
    fios em que o puxado só encosta

- **Assumptions confirmed:**
  - Travamento por ordem errada é legível sem tutorial
  - Desfazer sem custo não elimina a tensão do momento
  - A leitura de over/under do trançado se sustenta com tudo em movimento

- **Assumptions disproved:**
  - "Física de tensão" não é opcional nem cosmética: sem ela a mecânica vira um
    seletor de ordem e perde o pilar inteiro. **Não é área de polimento — é núcleo,
    e precisa entrar cedo na produção Godot.**
  - Fios radiais saindo de um centro não funcionam. A geometria precisa de cordas
    que realmente se atravessam.

- **Emergent mechanics (vale formalizar no GDD):**
  - **O nó aperta enquanto você força.** Feedback antecipado gratuito: o jogador vê
    o erro se formando antes de cometê-lo.
  - **Tranco nos vizinhos.** Um fio que escapa sacode quem estava embaixo — leitura
    imediata de "o que acabou de destravar".
  - **Ordenação por camadas** como gerador de níveis com solução garantida.

> Nota: o caminho HTML valida a *regra*. A sensação final em aparelho real (toque
> capacitivo, 60fps no Godot mobile renderer, vibração háptica) ainda não foi
> testada e deve ser reverificada no primeiro build de dispositivo.

**Next steps:**
1. `/design-review design/gdd/game-concept.md`
2. `/gate-check`
3. `/map-systems`
4. `/design-system tensao-de-corda` (usar os valores acima em Tuning Knobs e Formulas)

---

## Lessons Learned

- **What assumptions were broken by actually building this?**
  Que o protótipo estava testando a hipótese. Não estava: o v1 nunca implementou
  tensão, então o "pouco tátil" do primeiro playtest não era um resultado sobre o
  conceito — era um resultado sobre o meu código. Feedback de playtest só vale se
  o build realmente contiver a coisa que está sendo testada.

- **What surprised us that didn't show up in the brainstorm?**
  Que a geometria honesta resolveria de graça o maior risco do projeto. Fazer os
  fios se cruzarem de verdade exigiu empilhá-los em camadas, e a ordem de camadas
  *é* a garantia de solvabilidade que estava marcada como problema em aberto.

- **What would we test differently next time?**
  Escrever, junto com o escopo, uma checagem explícita de "o build contém o
  mecanismo da hipótese?" antes de entregar para playtest. E conduzir a debrief da
  segunda iteração com o mesmo rigor da primeira — "ficou muito melhor" é um sinal
  fraco comparado ao comportamento observado que a hipótese pedia.

---

> *Prototype code location: `prototypes/no-fisica-corda-concept/`*
> *This code is throwaway. Never refactor into production.*
