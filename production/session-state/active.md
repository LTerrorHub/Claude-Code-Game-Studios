# Active Session State

<!-- STATUS -->
Epic: Concept Validation
Feature: Nó Vivo — Física de Corda
Task: Concept prototype (HTML path)
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

## Progress

- [x] Phase 1-4: Hypothesis, path, and scope confirmed
- [x] Phase 5: Implemented `prototypes/no-fisica-corda-concept/prototype.html` — smoke-tested headless (Playwright), lock/undo/solve logic verified, no JS errors
- [ ] Phase 6: Playtest debrief (awaiting user's hands-on session)
- [ ] Phase 7: Generate REPORT.md
- [ ] Phase 8: Creative director review (skipped — lean mode)
- [ ] Phase 9: Summary and next steps

## Files Being Worked On

- `design/gdd/game-concept.md` (source of truth for concept)
- `prototypes/no-fisica-corda-concept/prototype.html` (to be created)
