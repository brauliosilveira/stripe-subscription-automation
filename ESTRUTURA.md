# Estrutura do projeto

Este arquivo define a estrutura obrigatória deste projeto. Ele foi criado a
partir de `_templates/projeto/ESTRUTURA.md`; mudanças de regra devem começar
no template e ser adotadas conscientemente pelos projetos existentes.

## Princípio

Cada tipo de informação tem um lugar. A raiz apresenta o projeto; `context/`
registra o estado operacional atual; `docs/` guarda planejamento e
especificações extensas; `design-system/` define o que é visual; o código e a
infraestrutura ficam em diretórios próprios da stack.

Não crie diretórios vazios nem force uma stack única. Uma pasta só nasce quando
houver conteúdo que pertence a ela.

## Estrutura base

```text
projeto/
├─ README.md              # porta de entrada: propósito, estado e navegação
├─ AGENTS.md              # regras locais de trabalho
├─ ESTRUTURA.md           # esta regra
├─ context/               # contexto operacional, conciso e vigente
│  ├─ product.md
│  ├─ architecture.md
│  ├─ stack.md
│  ├─ glossary.md
│  ├─ decisions/          # ADRs e decisões aceitas
│  └─ references/         # fichas autorais de fontes exclusivas do projeto
├─ docs/                  # planejamento, especificações e histórico útil
├─ design-system/         # apenas decisões visuais locais
└─ <código e infra>/      # diretórios da stack, quando existirem
```

`CLAUDE.md` pode existir somente como ponteiro para `AGENTS.md`. Arquivos de
entrada exigidos por uma ferramenta (por exemplo `PRODUCT.md`) podem continuar
na raiz quando essa ferramenta depender desse caminho.

## O que pertence a cada área

### `context/`

Guarda apenas o que alguém precisa ler para trabalhar corretamente agora:
produto, arquitetura, stack, glossário, decisões e fichas de referências do
projeto. Relatórios operacionais só permanecem aqui enquanto forem contexto
ativo; depois viram documento histórico ou são descartados conforme a política
do projeto.

### `docs/`

Guarda materiais longos de planejamento e especificação. Use subpastas apenas
quando ajudarem a navegação, por exemplo `docs/product/`, `docs/planning/`,
`docs/integrations/`, `docs/reports/` ou `docs/archive/`. Todo `docs/` com mais
de um documento deve ter um `docs/README.md` que explique o conteúdo e aponte
para os documentos principais.

Não colocar em `context/docs/`: roadmap, plano de execução, estudo, pesquisa,
briefing extenso, especificação de módulo, relatório histórico ou backup.

### `design-system/`

Contém somente o overlay visual específico deste projeto. A identidade da
organização continua no `design-system/` ancestral. Documentação de produto,
planejamento e referências visuais não pertencem a esta pasta.

### Código, infraestrutura e artefatos

Código deve usar a convenção natural da stack, como `app/`, `src/`, `server/`,
`shared/`, `apps/`, `packages/`, `supabase/`, `worker/`, `scripts/` ou `tests/`.
Builds, caches, dependências instaladas, logs e arquivos com segredos não são
documentação nem fonte de verdade e devem ficar ignorados pelo Git. Materiais
fonte e arquivos grandes permanecem em `D:\aios_files`.

## Regras de mudança

1. Antes de mover um documento, encontre e atualize todos os links que apontam
   para ele.
2. Preserve histórico útil em `docs/archive/`; não duplique o mesmo documento
   em `context/` e `docs/`.
3. Mantenha o `README.md` curto e verdadeiro. Ele deve apontar para `context/`,
   `docs/`, `design-system/` e para o guia de execução quando houver app.
4. Não usar esta estrutura para esconder pendências: estado e responsável da
   frente pertencem ao `docs/project-registry.md` do hub.
5. Mudança funcional continua seguindo versionamento e fluxo Git do projeto;
   reorganização documental não altera versão do aplicativo.
