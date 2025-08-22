### uv Field Manual

## 0 — Sanity
```bash
uv --version
```

## 1 — Workflows

**Project:**
```bash
uv init myproj && cd myproj
uv add ruff pytest httpx
uv run pytest -q && uv lock && uv sync --locked
```

**Script:**
```bash
uv run hello.py
uv add --script hello.py rich
uv run --with rich hello.py
```

**Tools:**
```bash
uvx ruff check .
uv tool install ruff && uv tool list && uv tool update --all
```

**Python:**
```bash
uv python install 3.10 3.11 3.12
uv python pin 3.12
uv run --python 3.10 script.py
```

**Legacy:**
```bash
uv venv .venv && source .venv/bin/activate
uv pip install/sync -r requirements.txt
```

## 2 — Performance
| Env | Purpose | Value |
|-|-|-|
| UV_CONCURRENT_DOWNLOADS | parallel | 16-32 |
| UV_CONCURRENT_INSTALLS | parallel | CPU_CORES |
| UV_OFFLINE | cache-only | 1 |
| UV_INDEX_URL | mirror | https://… |
| UV_PYTHON | pin Python | 3.11 |

```bash
uv cache dir && uv cache info && uv cache clean
```

## 3 — CI/Docker

**GHA:**
```yaml
- uses: astral-sh/setup-uv@v5
- run: uv python install && uv sync --locked && uv run pytest
```

**Docker:**
```dockerfile
FROM ghcr.io/astral-sh/uv:0.7.4 AS uv
FROM python:3.12-slim
COPY --from=uv /usr/local/bin/uv /usr/local/bin/uv
COPY pyproject.toml uv.lock /app/
WORKDIR /app
RUN uv sync --production --locked
COPY . /app
CMD ["uv","run","python","-m","myapp"]
```

## 4 — Migration
| From | To |
|-|-|
| python -m venv | uv venv |
| pip install | uv pip install |
| pip-tools compile | uv lock |
| pipx run | uvx / uv tool run |
| poetry add | uv add |
| pyenv install | uv python install |

## 5 — Troubleshooting
| Issue | Fix |
|-|-|
| Python not found | uv python install X.Y / UV_PYTHON |
| Proxy issues | UV_HTTP_TIMEOUT=120 UV_INDEX_URL=… |
| Build errors | unset UV_NO_BUILD_ISOLATION |
| Need fresh env | uv cache clean && rm -rf .venv && uv sync |
| Unknown | RUST_LOG=debug uv … |

## 6 — Pitch
Faster dependency management; universal lockfile; multi‑platform parity.

## 7 — Cheat Sheet
```bash
# new project
uv init myproj && cd myproj && uv add requests rich

# test & deploy
uv run python -m myproj && uv lock && uv sync --locked

# tools
uvx ruff check . && uv tool install pre-commit

# Python
uv python install 3.12 && uv python pin 3.12
```
