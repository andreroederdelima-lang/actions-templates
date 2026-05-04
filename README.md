# actions-templates

Workflows GitHub Actions reusable centralizados pros projetos do `andreroederdelima-lang`.

**Mexer aqui propaga pra todos os repos que consomem.**

## Workflows disponíveis

| Workflow | Pra quê | Inputs principais |
|----------|---------|-------------------|
| `.github/workflows/node-ci.yml` | TypeScript/JS (auto-detecta pnpm/npm/bun) | `node-version`, `tests-strict`, `format-strict` |
| `.github/workflows/python-ci.yml` | Python (pip/poetry, ruff, mypy, pytest) | `python-version`, `tests-strict` |
| `.github/workflows/security.yml` | Audit (npm/pnpm/pip-audit) + gitleaks + CodeQL | `audit-level`, `codeql-language` |

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
