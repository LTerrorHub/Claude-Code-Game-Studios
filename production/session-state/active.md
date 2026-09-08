# Active Session State

<!-- STATUS -->
Epic: Concept Validation
Feature: Nó Vivo — Física de Corda
Task: conceito revisado e decisões travadas; aguardando playtest externo
<!-- /STATUS -->

## Current Task

Running `/prototype nó-física-corda` — concept prototype to validate the core
untangling mechanic before writing GDDs.

## Hypothesis

Se o jogador puxa pontas de fios de lã com física real de tensão (onde a ordem
errada pode travar o nó, exigindo desfazer), ele vai sentir um momento genuíno
de "eu consigo" — vamos saber que é verdade se os testadores tentarem novamente
um nó travado por vontade própria (sem serem instruídos) e conseguirem explicar
verbalmente por que uma puxada falhou.

**Riskiest assumption**: o mecanismo de travamento parece justo/legível ou
arbitrário/frustrante.

## Path

HTML (browser prototype) — physics is drag-based, not reflex-timing-sensitive,
so browser latency doesn't compromise the test.

## Scope

- 1 nó com 3-4 fios coloridos emaranhados (física tipo Verlet)
- Arrastar ponta solta o fio; ordem errada trava o nó (cordas ainda cruzadas)
- Desfazer instantâneo, sem custo
- Feedback simples: cor de destaque + som ao soltar, vibração visual ao travar
- Cortado: Vila/meta, múltiplos níveis, combo, menu, arte polida

## Playtest 1 (v1) — resultado

**PARCIALMENTE CONFIRMADA.** O travamento é legível ("entendi o travamento") — a
suposição mais arriscada caiu. Mas a puxada foi julgada "pouco tátil", atingindo o
pilar nº 1 (Tensão Tátil Real). Causa apurada no código: o v1 não simulava tensão
nenhuma — `visualProgress = clamp(proj/MAXPULL)`, um slider linear. Os fios também
não se cruzavam de verdade; o cruzamento era uma barra pintada por cima.

Lacunas apontadas pelo jogador: sem resistência, o resto do nó não reage, o fio não
deforma. (Feedback durante a puxada NÃO foi apontado como problema.)

## Playtest 2 (v2) — pendente

v2 implementado: cadeia de 26 pontos por fio com Verlet + restrições de distância,
parede de esticamento absoluta (62px), cruzamentos derivados de interseções
geométricas reais, nó que se aperta enquanto você força, deformação (afinamento +
fibras se espaçando), oscilação vinda da física, trava que fixa o aperto até Desfazer.

Solvabilidade agora é garantida por construção: os fios são ordenados em camadas
(ordem total), então sempre existe um fio no topo que está livre. Isso ataca o
"maior risco" registrado no game-concept.md.

Medições headless: fio livre cede com 64px de dedo; fio preso trava em 62px de
esticamento mesmo com 240px de arrasto, e soltar ali trava o nó.

## Progress

- [x] Phase 1-4: Hypothesis, path, and scope confirmed
- [x] Phase 5: Implemented `prototypes/no-fisica-corda-concept/prototype.html` — smoke-tested headless (Playwright), lock/undo/solve logic verified, no JS errors
- [x] Phase 6a: Playtest 1 debrief — PARCIALMENTE CONFIRMADA (travamento legível, tato ausente)
- [x] Phase 5b: v2 com simulação de corda real — smoke test headless passa (3 puxadas certas, 1 travamento, 0 erros)
- [x] Phase 6b: Segundo playtest — "ficou muito melhor", veredito PROCEED
- [x] Phase 7: REPORT.md escrito + prototypes/index.md criado
- [x] Phase 8/9: /prototype encerrado
- [x] /design-review (full, 6 especialistas + creative-director) — MAJOR REVISION NEEDED, escopo XL
- [x] REPORT.md emendado: veredito → PROCEED WITH CONDITION
- [x] design/gdd/reviews/game-concept-review-log.md criado
- [ ] **BLOQUEANTE: playtest externo do v2** — 2-3 pessoas não avisadas, ≥1 no celular
- [x] 4 decisões travadas: D1 aprender-primeiro / D2 combo-é-a-aposta + enquadramento sensorial / D3 economia completa da Vila (contra recomendação) / D4 GDScript+C# (contra recomendação)
- [x] game-concept.md revisado — 14 edições aplicadas
- [ ] ADR da decisão de linguagem (/architecture-decision)
- [ ] /gate-check → /map-systems

## Files Being Worked On

- `design/gdd/game-concept.md` (source of truth for concept)
- `prototypes/no-fisica-corda-concept/prototype.html` (to be created)
