# ADR-0001: Linguagem de implementação do MVP — GDScript + C#

## Status

**Proposed**

Esta decisão não pode ser promovida a **Accepted** sem os itens de verificação
listados em *Validation Criteria*. Nenhum deles pode ser confirmado por leitura de
documentação — todos exigem um export real em aparelho real.

## Date

2026-09-08

## Engine Compatibility

| Field | Value |
|-------|-------|
| **Engine** | Godot 4.6 (lançado jan/2026) |
| **Domain** | Core / Scripting |
| **Knowledge Risk** | **HIGH** — versão pós-cutoff do modelo (treino cobre ~4.3) |
| **References Consulted** | `docs/engine-reference/godot/VERSION.md`, `breaking-changes.md`, `current-best-practices.md`, `deprecated-apis.md` |
| **Post-Cutoff APIs Used** | Nenhuma API pós-cutoff é *usada* por esta decisão. Quatro fatos pós-cutoff a **informam**, todos verificados contra `breaking-changes.md`: GDScript ganhou argumentos variádicos e `@abstract` (4.5); *script backtracing* em builds de Release (4.5); extração automática de strings de tradução em C# (4.6); Android exige suporte a páginas de 16KB para publicar mirando Android 15+ (4.5). |
| **Verification Required** | Comportamento dos export templates .NET em iOS e Android no 4.6; delta real de tamanho de binário com .NET habilitado; se o requisito de páginas de 16KB do Android interage com o runtime .NET. **A biblioteca de referência deste projeto não tem documento de módulo para scripting/.NET — nada disso pode ser afirmado a partir da documentação disponível.** |

Nota de compatibilidade: a mudança do 4.6 que tornou **Jolt** o motor de física
padrão afeta apenas física **3D**. Este projeto usa Godot Physics 2D (built-in) +
simulação customizada, conforme `technical-preferences.md`. Sem conflito.

## ADR Dependencies

| Field | Value |
|-------|-------|
| **Depends On** | None — é o primeiro ADR do projeto |
| **Enables** | ADR futuro de arquitetura da simulação de corda; ADR de estratégia de testes |
| **Blocks** | Qualquer story de implementação do sistema de tensão de fio |
| **Ordering Note** | Deve estar **Accepted** antes da primeira sprint de código. Enquanto estiver *Proposed*, nenhuma story de solver pode sair de Blocked. |

## Context

### Problem Statement

Em que linguagem o MVP é construído? A decisão precisa ser tomada agora porque
determina o toolchain, o pipeline de export para duas plataformas móveis, a
estratégia de testes automatizados e as convenções de código — todos fixados antes
da primeira story.

O gatilho é uma divergência real. A preferência registrada pelo desenvolvedor em
`.claude/docs/technical-preferences.md` roteia **C# para "sistemas críticos de
performance"**. A revisão de conceito de 2026-09-08 apurou que a simulação de corda
**não atinge esse critério**: no pior caso são ~10 fios × 26 pontos = ~260 pontos,
com 6 relaxações × 2 substeps por quadro. É trabalho trivial para GDScript tipado
operando sobre `PackedVector2Array`.

Ou seja: **pela política registrada, nenhuma condição dispara C#.** Um MVP 100%
GDScript já cumpriria a preferência do desenvolvedor sem alterá-la.

O desenvolvedor optou por C# mesmo assim, com a justificativa declarada de
**aprender C# junto com o projeto**. Este ADR registra essa escolha como decisão
consciente e documenta o custo que ela carrega, para que ele não seja redescoberto
como surpresa no meio da produção.

### Constraints

- Desenvolvedor solo, primeiro jogo, primeiro projeto em Godot
- Alvo mobile (iOS + Android), renderizador Mobile, orçamento de ~100 draw calls
- Orçamento de quadro 16,6ms (60fps, fallback 30fps em aparelhos fracos)
- Postura de projeto **"aprender primeiro"** (decisão D1 do conceito): receita é
  secundária, terminar e publicar é o objetivo
- Godot 4.6 tem 1 mês de idade — documentação da comunidade ainda é escassa e a
  existente descreve versões anteriores
- `.claude/docs/coding-standards.md` exige teste automatizado **bloqueante** para
  sistemas de lógica, e nomeia **GUT** como framework — GUT é GDScript-only

### Requirements

- A simulação de corda deve caber no orçamento de 16,6ms no aparelho-alvo mais fraco
- Os valores de tuning devem ser ajustáveis sem ciclo de recompilação (o valor do
  protótipo veio de iteração rápida)
- A matemática do solver deve ser testável automaticamente em CI
- O tamanho do binário importa: instalação afeta conversão em jogo casual mobile
- A escolha não pode inviabilizar troca de linguagem depois

## Decision

**O MVP é implementado em GDScript + C#.** Gameplay, UI e cola em GDScript; a
simulação de tensão de fio em C#.

### Regra de fronteira (vinculante)

A versão inicial desta regra — "uma chamada por fio por quadro devolvendo um
`PackedVector2Array`" — foi **rejeitada pelo godot-specialist como subótima** e
substituída pela seguinte, que é estritamente melhor:

> **O script do nó Strand é C#.** Ele possui o próprio tick de física e escreve
> diretamente no seu `Line2D`/nó de geometria filho. O motor chama `_PhysicsProcess`
> direto no objeto C# — isso é despacho virtual, **não** uma chamada marshalada
> GDScript→C#. Resultado: **zero travessias de fronteira no laço de 60Hz.**
>
> O GDScript nunca toca em estado de corda por quadro. O único contato legítimo por
> frame é **entrada**: uma chamada `SetDragTarget(Vector2)` por evento de toque, no
> único fio sendo arrastado — não por fio, não por quadro, e um `Vector2`, não um array.
>
> Transições de estado (travou / resolveu / soltou) são **sinais C#** que o GDScript
> assina. Orientado a evento, nunca por polling.

**Proibido**: qualquer fronteira cruzada por ponto por quadro. O marshaling de
`Variant` custaria mais que a matemática economiza.

### Estratégia de teste (resolve a lacuna do GUT)

A matemática pura do solver — integração de Verlet, restrições de distância, parede
de esticamento, detecção de travamento — vive numa **classe C# simples, sem
dependência de Godot**, exercitada por um runner .NET headless no CI. O nó Godot é
uma casca fina que chama essa classe.

Isso cumpre o padrão de teste bloqueante do projeto sem depender do GUT, e força um
design mais limpo: a matemática fica isolada da engine.

### Key Interfaces

```
Strand (C#, Node2D)
├─ Line2D (filho, escrito diretamente pelo C#)
├─ SetDragTarget(Vector2)            ← única entrada por frame, só no fio arrastado
├─ signal StrandLocked(strandId)     ← GDScript assina
├─ signal StrandFreed(strandId)
└─ [Export] tuning: SnapPx, WallPx, LockPx, Damping, TipSpring, PointCount

RopeSolver (C# puro, sem Godot)      ← alvo dos testes unitários
└─ Step(points, constraints, dt) → void
```

Todos os valores de tuning são `[Export]` para que retunar **não** exija recompilação
— mitigação direta do risco de iteração lenta (ver Risks).

## Alternatives Considered

### Alternativa 1: GDScript puro, C# apenas mediante evidência de profiling

- **Descrição**: MVP 100% GDScript tipado. C# vira contingência disparada por
  medição em aparelho real, não por suposição de arquitetura.
- **Prós**: cumpre a política registrada sem alterá-la (nenhuma condição dispara C#);
  um toolchain; binário menor; recarregamento vivo rápido, que é exatamente o ciclo
  de iteração que produziu os valores de tuning do protótipo; sem depuração
  cross-language no primeiro projeto.
- **Contras**: não atende ao objetivo declarado de aprender C#; migrar depois exige
  reescrever o solver (embora a regra de fronteira acima torne isso local).
- **Motivo da rejeição**: **rejeitada pelo desenvolvedor, contra a recomendação
  técnica.** Era a opção recomendada pelo godot-specialist e pelo creative-director.
  A justificativa aceita é aprendizado, não desempenho. Registrada aqui para que a
  troca fique explícita e reversível.

### Alternativa 2: C++ via GDExtension para o solver

- **Descrição**: solver nativo em C++ via godot-cpp, chamado a partir de GDScript.
- **Prós**: sem runtime Mono/CoreCLR embarcado — que é o custo dominante de tamanho
  de binário e tempo de inicialização em mobile, **não o solver**; sem coletor de
  lixo; sem overhead de interop de nenhum tipo.
- **Contras**: Android exige NDK + cross-compilation para `arm64-v8a`/`armeabi-v7a`
  com ABI casada com o build exato do 4.6; iOS exige build de biblioteca estática via
  Xcode e embutimento manual no projeto gerado no export — passo extra que C# não
  tem. **A estabilidade de ABI do godot-cpp para 4.6 não pôde ser verificada**: a
  biblioteca de referência do projeto não tem documento de GDExtension e a versão tem
  1 mês.
- **Motivo da rejeição**: quatro razões, todas registradas: **(a)** necessidade de
  performance é zero — o protótipo já mostrou folga larga; **(b)** o custo de
  toolchain nas duas plataformas recai sobre um dev solo que já está absorvendo Godot
  e C# pela primeira vez; **(c)** adiciona uma terceira incógnita não verificada sobre
  duas que já existem; **(d)** **não serve ao objetivo declarado** — GDExtension é
  C++/Rust e não ensina C#.

  Tensão importante registrada: o principal atrativo do GDExtension (não embarcar o
  runtime .NET) **só compensa se C# for abandonado**, não adicionado. "GDScript +
  GDExtension, sem C#" é uma quarta combinação coerente — mas contradiz frontalmente
  a razão do desenvolvedor para escolher C#. E as três juntas não é escopo sensato
  para um dev solo num solver de 260 pontos.

### Alternativa 3: GDScript + C# desde o início — **ESCOLHIDA**

Ver *Decision*.

## Consequences

### Positive

- O desenvolvedor aprende C# num contexto real, com um sistema pequeno e isolado —
  se é para aprender, este é um bom lugar: o solver tem fronteira estreita e é o
  único candidato legítimo.
- A regra de fronteira (nó C# dono do próprio tick) é mais rápida que a alternativa
  que eu havia rascunhado, e desacopla o solver do GDScript por completo.
- A separação `RopeSolver` puro + casca de nó produz matemática testável — o projeto
  ganha cobertura automatizada real onde mais importa.
- A interface pública (propriedades, métodos, sinais) torna a **linguagem de
  implementação um detalhe substituível**. Trocar por GDScript ou GDExtension depois
  não obriga a reescrever o lado GDScript.

### Negative

Custos aceitos conscientemente:

- Segundo toolchain (.NET SDK) e complexidade de export templates para iOS e Android
- Binário maior — em jogo casual mobile, tamanho de instalação afeta conversão
- Depuração cross-language no primeiro projeto
- Duas convenções de nomenclatura para manter sozinho (já documentadas em
  `technical-preferences.md`)
- Recarregamento de domínio C# é mais lento que o live reload do GDScript, degradando
  exatamente o ciclo de iteração rápida que produziu os valores do protótipo

### Risks

| Risco | Mitigação |
|---|---|
| **Pausas de GC do Mono no Android durante arrasto ativo** — custo geral de C# no Android, independente do solver | Código do solver livre de alocação por quadro: arrays pré-dimensionados, sem LINQ, sem `new` por frame. Verificar com profiler Android em aparelho fraco real **durante um arrasto**, não no editor. |
| **Atrito de hot-reload do C# desacelera a iteração de tuning** | Todos os valores de tuning como `[Export]`, editáveis no Inspector. Retunar não recompila. |
| **Lacuna de framework de teste** — GUT é GDScript-only e não exercita o solver C#; o projeto ficaria silenciosamente fora do próprio padrão | Resolvido nesta ADR: matemática pura em classe C# sem Godot + runner .NET headless em CI. |
| **Pinagem de export templates** — 4.6 tem 1 mês; templates .NET para duas plataformas devem casar com o build exato, dobrando a superfície de erro | Fixar versão exata do editor e dos templates num documento do projeto. Fazer um *spike* de export "hello world" com C# nas duas plataformas **antes** de qualquer código de produção depender do pipeline. |
| **Risco tardio de submissão iOS** por restrições de runtime a código dinâmico — descoberto muito tarde por padrão, sem rede de proteção para dev solo | Colocar um build real na esteira TestFlight **antes** do investimento profundo em produção, não na hora de publicar. |
| **Conhecimento comunitário desatualizado** — tutoriais de "Godot C# mobile export" descrevem comportamento pré-4.6 | Tratar `docs/engine-reference/godot/` e docs.godotengine.org como as únicas fontes confiáveis. Qualquer conselho da web sobre C#/export precisa ser reverificado contra 4.6 antes de virar ação. |

## GDD Requirements Addressed

| GDD System | Requirement | How This ADR Addresses It |
|------------|-------------|--------------------------|
| `design/gdd/game-concept.md` | "Simulação de tensão do fio" listada como sistema primário (Core Mechanics) | Define a linguagem, a fronteira e a estratégia de teste desse sistema |
| `design/gdd/game-concept.md` | Orçamento 60fps / 16,6ms em mobile (Technical Considerations) | Regra de fronteira elimina marshaling do laço de 60Hz; profiling em aparelho real é critério de aceitação |
| `design/gdd/game-concept.md` | Valores de tuning "reexpressar como razões antes de entrar em qualquer GDD" | Tuning exposto como `[Export]`, ajustável sem recompilar |

## Performance Implications

- **CPU**: ~260 pontos × 12 passadas/quadro no pior caso. Trivial em qualquer das três
  linguagens. **Não medido em aparelho real** — é critério de aceitação, não afirmação.
- **Memory**: runtime .NET embarcado adiciona overhead fixo. Magnitude **não verificada**
  para 4.6 mobile.
- **Load Time**: inicialização do runtime .NET adiciona custo fixo. **Não verificado.**
- **Network**: não aplicável (single-player, sem rede no MVP).

## Migration Plan

Não há código de produção ainda — nada a migrar. O protótipo em
`prototypes/no-fisica-corda-concept/` é descartável e **não** deve ser refatorado
para dentro de `src/`; ele serve como especificação de comportamento e fonte dos
valores de tuning iniciais, não como base de código.

Se a decisão for revertida para GDScript puro após o profiling, o impacto é local:
a interface pública do nó Strand (propriedades, métodos, sinais) permanece idêntica,
e só o corpo do solver muda.

## Validation Criteria

Esta ADR permanece **Proposed** até que **todos** os itens abaixo sejam verificados
em hardware/export real. Nenhum é confirmável por documentação.

1. Export real (iOS **e** Android, não "run" no editor) de um projeto mínimo
   GDScript+C#, confirmando que o build com .NET instala e roda no aparelho-alvo.
2. Delta de tamanho de binário medido entre export só-GDScript e o mesmo projeto com
   C# habilitado — resolve se a preocupação de conversão de instalação é material ou
   hipotética.
3. Uma passada de profiling do solver real (depois de escrito) no aparelho-alvo mais
   fraco, no pior caso (~10 fios, 260 pontos, arrasto ativo), confirmado dentro dos
   16,6ms — não a extrapolação dos números de navegador do protótipo.
4. Confirmação de que o requisito de páginas de 16KB do Android não conflita com o
   empacotamento do runtime .NET no 4.6.
5. Comando de teste headless rodável em CI para a abordagem de teste C# escolhida,
   **verificado executando de verdade** antes de ser tratado como portão de teste.
6. Confirmação explícita de que GDExtension não é necessário como plano B — ou seja,
   que o caminho de export C# para iOS/Android funciona **agora**, e não descoberto
   no meio da produção.

## Related Decisions

- `design/gdd/game-concept.md` — decisão D4 (linguagem), com a recomendação contrária preservada
- `design/gdd/reviews/game-concept-review-log.md` — revisão que originou esta ADR
- `prototypes/no-fisica-corda-concept/REPORT.md` — origem dos parâmetros de simulação
- `.claude/docs/technical-preferences.md` — política de roteamento de linguagem (não alterada por esta ADR)
