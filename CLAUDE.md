# Gestor de Tráfego Turbo — instruções pro Claude

## Se te pediram pra INSTALAR o gestor de tráfego nesta máquina

**Baixar o repositório (se ainda não estiver baixado):** git NÃO é pré-requisito. Se `command -v git` falhar, **não dispare a instalação do Xcode Command Line Tools** (popup de ~2 GB que trava o fluxo) — baixe o ZIP com o curl nativo do macOS:

```bash
curl -L https://github.com/Turbo-Academy/gestor-trafego-turbo/archive/refs/heads/main.zip -o gestor.zip
unzip -q gestor.zip && rm gestor.zip
# cria a pasta gestor-trafego-turbo-main — trabalhe dentro dela
```

Com git presente, `git clone https://github.com/Turbo-Academy/gestor-trafego-turbo.git` é equivalente.

**Instalar:** rode `bash install.sh` (idempotente — copia o agente `trafego-turbo` e as 18 skills pra `~/.claude/`). Não precisa de senha nem de Homebrew.

**Opcional — leitura de páginas web** (a skill `leitura-web-turbo` funciona sem isto, degradando pro WebFetch): `bash tools/instalar-scrapling.sh` instala o Scrapling (~400 MB de venv + ~1,1 GB de navegadores) e registra o MCP `scrapling` no escopo user. **Pergunte antes** — é pesado e nem todo mundo precisa.

**No fim, avise o usuário:** skills, agente e MCPs só carregam em **sessão nova** do Claude Code — a sessão da instalação não enxerga o que ela mesma instalou. Conferir: `/skills` lista as skills e o agente responde a "Use o agente trafego-turbo…".

O manual humano dessa instalação é o [INSTALACAO-DO-ZERO.md](INSTALACAO-DO-ZERO.md).

## Meta Ads CLI — configuração desta máquina (Windows 11 + WSL Ubuntu 26.04)

A CLI `meta-ads` roda dentro do WSL (não no Windows nativo — sem wheels pra Windows).

**Como chamar a CLI no Claude Code (padrão com perfil):**
```bash
META_PROFILE=kim-sisbio wsl.exe -d Ubuntu -- bash -c 'source ~/.bashrc && meta --output json ads campaign list'
```

**Perfis configurados** (`~/.config/meta-ads/`):

| Perfil | Conta | ID | BM |
|---|---|---|---|
| `neto01` | Neto01 Reserva | act_752878252141728 | BM1 Neto (258595245005064) |
| `bm02-ca01` | CA01BM02 | act_299951728292813 | BM02 Neto (3845516972194077) |
| `bm02-ca02` | CA02_BM02_LIBRAS_1A7D | act_1538362221246228 | BM02 Neto (3845516972194077) |
| `kim-sisbio` | Sisbio | act_952932413377197 | DrRodrigoKim (379461895880472) |
| `kim-metodo` | Método | act_3497083207034532 | DrRodrigoKim (379461895880472) |

**Contas extras na mesma BM** (fora da tabela acima, mas acessíveis com o token de um perfil já
configurado, usando `--ad-account-id` sem trocar de perfil — `meta ads --ad-account-id
act_XXXXXXXXXXXXXXX <comando>`):

| Conta | ID | Acessível via perfil |
|---|---|---|
| CA03_BM02_INL \| LB21 VSL \| Perpétuo | act_1745113480051846 | `bm02-ca01` (BM02 Neto) |

**Wrapper multi-perfil** (`~/.local/bin/meta-env`): carrega automaticamente o `.env.<perfil>` correto.

**Forma canônica de chamar qualquer conta:**
```bash
# Trocar perfil ativo no Ubuntu
meta-conta kim-sisbio && meta --output json ads campaign list

# Ou inline (sem mudar perfil global)
META_PROFILE=bm02-ca01 wsl.exe -d Ubuntu -- bash -c 'source ~/.bashrc && meta --output json ads adaccount get'
```

**Credenciais:** em `~/.config/meta-ads/.env.<perfil>` dentro do WSL (fora do repo — nunca comitar).

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
- O repo é público. As skills são cópias das canônicas do Squad Turbo (repo squad-turbo-2026) — melhorias entram por lá e são sincronizadas pra cá; não edite as cópias daqui à mão.
