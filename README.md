# actions-templates

Workflows GitHub Actions reusable centralizados pros projetos do `andreroederdelima-lang`.

**Mexer aqui propaga pra todos os repos que consomem.**

## Workflows disponíveis

| Workflow | Pra quê | Inputs principais |
|----------|---------|-------------------|
| `.github/workflows/node-ci.yml` | TypeScript/JS (auto-detecta pnpm/npm/bun) | `node-version`, `tests-strict`, `format-strict` |
| `.github/workflows/python-ci.yml` | Python (pip/poetry, ruff, mypy, pytest) | `python-version`, `tests-strict` |
| `.github/workflows/security.yml` | Audit (npm/pnpm/pip-audit) + gitleaks + CodeQL | `audit-level`, `codeql-language` |
| `.github/workflows/e2e.yml` | Playwright + smoke HTTP (gate prod) | `node-version`, `browser`, `target-url` |

## Fluxo dev → stage → main (gates progressivos)

Os consumer templates já vêm configurados pra rodar em PRs contra `dev`, `stage` e `main` com strictness diferente:

| Promoção | tests-strict | format-strict | audit-level | E2E |
|----------|:------------:|:-------------:|:-----------:|:---:|
| qualquer → `dev`   | falso (warning) | falso | high | não |
| `dev` → `stage`    | **verdadeiro** | falso | high | não |
| `stage` → `main`   | **verdadeiro** | **verdadeiro** | **low** | **sim** |

A intenção: bug deve ser pego antes de prod. PR pra dev pode entrar com warnings (rapidez), pra stage tem que ser verde, pra main é gate completo + Playwright + smoke contra ambiente stage.

Pra ativar em um repo, basta criar as branches `dev` e `stage`. Sem elas, o fluxo continua sendo só PR pra main (compatibilidade total).

## Como aplicar num projeto

### Maneira 1 — manual (3 minutos)

1. Copia o arquivo apropriado de `templates/`:
   - Repo Node: `templates/node-consumer.yml`
   - Repo Python: `templates/python-consumer.yml`
2. Cola em `.github/workflows/ci.yml` do seu projeto
3. Commit, push, PR — CI roda automático

### Maneira 2 — automático (recomendado)

```bash
oc-aplicar-ci <repo-nome>            # detecta stack e aplica
oc-aplicar-ci <repo-nome> --auto     # com auto-merge ativado
```

Script em `/root/scripts/oc-aplicar-ci.sh` na VPS.

## Como funciona o `tests-strict` / `format-strict`

Por padrão `false`: testes ou formato falhos viram **warning** (CI continua verde, merge libera).
Quando você quiser virar gate duro: passar `true` no consumer.yml.

Útil pra quando o projeto tem dívida técnica (testes quebrados, código não formatado) e você quer
adicionar CI sem bloquear tudo de cara.

## Editando

Pra mudar comportamento em todos os projetos: edite os arquivos em `.github/workflows/` desse repo.
A próxima execução de CI em qualquer projeto consumidor já pega a versão nova (graças a `@main`).

Pra estabilidade extra, o consumer pode pinar versão: `@v1.0.0` em vez de `@main`.
