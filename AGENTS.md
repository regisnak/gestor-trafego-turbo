# Gestor de Tráfego Turbo — instruções pro Codex

## Se te pediram pra INSTALAR o gestor de tráfego nesta máquina

**Baixar o repositório (se ainda não estiver baixado):** git NÃO é pré-requisito. Se `command -v git` falhar, **não dispare a instalação do Xcode Command Line Tools** (popup de ~2 GB que trava o fluxo) — baixe o ZIP com o curl nativo do macOS:

```bash
curl -L https://github.com/Turbo-Academy/gestor-trafego-turbo/archive/refs/heads/main.zip -o gestor.zip
unzip -q gestor.zip && rm gestor.zip
# cria a pasta gestor-trafego-turbo-main — trabalhe dentro dela
```

Com git presente, `git clone https://github.com/Turbo-Academy/gestor-trafego-turbo.git` é equivalente.

**Instalar:** rode `bash install.sh` (idempotente — copia o agente `trafego-turbo` e as 18 skills pra `~/.Codex/`). Não precisa de senha nem de Homebrew.

**Opcional — leitura de páginas web** (a skill `leitura-web-turbo` funciona sem isto, degradando pro WebFetch): `bash tools/instalar-scrapling.sh` instala o Scrapling (~400 MB de venv + ~1,1 GB de navegadores) e registra o MCP `scrapling` no escopo user. **Pergunte antes** — é pesado e nem todo mundo precisa.

**No fim, avise o usuário:** skills, agente e MCPs só carregam em **sessão nova** do Codex — a sessão da instalação não enxerga o que ela mesma instalou. Conferir: `/skills` lista as skills e o agente responde a "Use o agente trafego-turbo…".

O manual humano dessa instalação é o [INSTALACAO-DO-ZERO.md](INSTALACAO-DO-ZERO.md).

## Meta Ads CLI — configuração desta máquina (Windows 11 + WSL Ubuntu 26.04)

A CLI `meta-ads` roda dentro do WSL (não no Windows nativo — sem wheels pra Windows).

**Como chamar a CLI no Codex:**
```bash
wsl.exe -d Ubuntu -- python3 -c "
import os, sys, subprocess
env_file = os.path.expanduser('~/.config/meta-ads/.env')
env = os.environ.copy()
with open(env_file) as f:
    for line in f:
        line = line.strip()
        if '=' in line and not line.startswith('#'):
            k, v = line.split('=', 1)
            v = v.strip().strip('\"').strip(\"'\")
            env[k.strip()] = v
meta = os.path.expanduser('~/.local/share/uv/tools/meta-ads/bin/meta')
result = subprocess.run([meta, '--output', 'json', 'ads'] + sys.argv[1:], env=env, capture_output=True, text=True)
print(result.stdout or result.stderr)
" -- SUBCOMANDO ARGS
```

Ou com o wrapper direto no Ubuntu:
```bash
wsl.exe -d Ubuntu -- bash -c 'source ~/.bashrc && meta --output json ads campaign list'
```

**Credenciais:** em `~/.config/meta-ads/.env` dentro do WSL (fora do repo — nunca comitar).
**Conta ativa:** `act_752878252141728` (Neto01 Reserva) · BM `3845516972194077`

**Regras invioláveis:**
- Campanha nova sempre nasce `status=PAUSED` — ativar é decisão do usuário
- `--output json` vai ANTES de `ads` (flag global do `meta`): `meta --output json ads campaign list`
- Comandos de escrita (`create`, `update`, `delete`, `pause`, `resume`) sempre pedem confirmação explícita antes de executar

**Comandos read-only (sem confirmação):**
- `meta --output json ads campaign list`
- `meta --output json ads adset list`
- `meta --output json ads ad list`
- `meta --output json ads insights get --date-preset last_7d`
- `meta --output json ads adaccount get`

**Comandos de escrita (sempre confirmar antes):**
- `meta ads campaign create ...` · `meta ads campaign update ...`
- `meta ads campaign pause ...` · `meta ads campaign resume ...`

## Sobre este repositório

- Agente de **tráfego pago** (Meta Ads + Google Ads) da Turbo Academy: 1 agente + 18 skills, roda **sem** o Squad Turbo completo. Comece pelo [README](README.md).
- Regras de segurança do agente: campanha nova nasce **PAUSED**; a CLI confirma toda escrita; token da Meta **nunca** entra no repo.
- O repo é público. As skills são cópias das canônicas do Squad Turbo (repo squad-turbo-lpsg-7.0) — melhorias entram por lá e são sincronizadas pra cá; não edite as cópias daqui à mão.
