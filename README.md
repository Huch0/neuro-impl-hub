# neuro-impl-hub 🔬🤖

A collaborative playground for:

1. **Reproducing** neural‑network papers (NLP, RL, etc.).
2. **Implementing & Testing** our own model variants.

> **Build • Test • Reproduce — all under reproducible Conda locks.**

---

## ✨ Key Features

| What                                | Why it matters                                                                                               |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| **Pixi‑powered multi‑env workflow** | Cleanly isolate per‑paper dependencies while sharing a single solver cache & lockfile.                       |
| **Deterministic `pixi.lock`**       | One commit pin = identical bits on every machine & CI runner.                                                |
| **Zero‑friction subshells**         | `pixi shell -e <env>` drops you into an already‑activated conda env; exit to leave—no `deactivate`.          |
| **CI‑ready**                        | Add a matrix job that runs `pixi install --locked -e <env>` to guarantee the repo always stays reproducible. |

---

## 🛠️ Quick Start

```bash
# 1) clone & bootstrap common dev tools
 git clone https://github.com/<you>/neuro-impl-hub.git
 cd neuro-impl-hub
 pixi install                       # solves & creates the *default* env

# 2) launch JupyterLab inside the default dev env
 pixi run jupyter lab
```

> *The first solve may take a minute; subsequent runs reuse the local cache.*

---

## 🌱 Repository Layout

```
├── README.md          ← you are here
├── pixi.toml          ← single manifest describing *all* envs & features
├── pixi.lock          ← frozen dependency graph (commit this!)
├── .pixi/             ← auto‑generated env folders (git‑ignored)
├── papers/            ← one sub‑dir per reproduced paper
│   └── <Paper_X>/
├── tutorials/         ← tutorials for libraries & techniques
│   └── <Tutorial_X>/
└── models/            ← your original experiments
```

---

## 🚀 Workflow Recipes

### 1. One‑time bootstrap (already done for you)

```bash
pixi init                              # wrote pixi.toml & pixi.lock
pixi add --feature dev python=3.12 jupyterlab pre-commit rich black
pixi workspace environment add default --feature dev --force
```

*Everything above is captured in the committed files; you only repeat it if you start a **fresh** repo.*

---

### 2. Spin‑up a new implementation (example: *DQN 2015*)

```bash
# make a home for the code
mkdir -p papers/DQN_2015 && cd papers/DQN_2015

# declare deps in a new *feature*
 pixi add --feature dqn torch=2.2 gymnasium=0.29

# map that feature to an installable environment
 pixi workspace environment add dqn --feature dqn

# jump into an interactive subshell
 pixi shell -e dqn
```

Pixi appends to **pixi.toml** automatically:

```toml
[feature.dqn.dependencies]
torch = "2.2.*"
gymnasium = "0.29.*"

[environments]
dqn = ["dqn"]
```

On first use Pixi materialises `.pixi/envs/dqn/`—fully isolated from other envs.

---

### 3. Update an environment & regen the lock

```bash
# while inside 'pixi shell -e dqn' …
 pixi add --feature dqn tensorboard     # modifies manifest *and* regenerates pixi.lock

# share the results
 git add pixi.toml pixi.lock
 git commit -m "Add TensorBoard to DQN env"
```

Every environment’s packages are tagged inside a **single** `pixi.lock`, so the repo never explodes with N× lockfiles.

---

### 4. Re‑create someone else’s env from the lockfile

```bash
git clone https://github.com/<you>/neuro-impl-hub.git
cd neuro-impl-hub

# deterministic, fail‑fast install
 pixi install --locked -e dqn

# run code inside that env
 pixi run -e dqn python train.py --config cfg.yaml
```

`--locked` aborts if your local `pixi.lock` is older than the committed one—eliminating “works‑on‑my‑machine” drift.

---

## 🧰 Pixi Command Cheatsheet

| Task                                           | Command                                        |
| ---------------------------------------------- | ---------------------------------------------- |
| List declared envs                             | `pixi workspace environment list`              |
| Remove an env def                              | `pixi workspace environment remove <name>`     |
| Enter an env                                   | `pixi shell -e <name>`                         |
| Run *cmd* inside env without going interactive | `pixi run -e <name> <cmd>`                     |
| Add a package to a feature                     | `pixi add --feature <feat> <pkg>=<ver>`        |
| Update packages for one feature                | `pixi update --feature <feat>`                 |
| Hard‑refresh env vs lock                       | `pixi install --locked -e <name> --revalidate` |

---

## 🤝 Contributing Guidelines

1. **One PR = one paper or feature.** Keep diffs readable.
2. **Always commit `pixi.toml` *and* `pixi.lock`.** CI will fail if they diverge.
3. **Prefer `pixi add --feature <feat>`** over editing the TOML by hand.
4. Write a minimal `README.md` inside each `papers/<Paper>` or `models/<Exp>` folder:

   * citation (BibTeX)
   * dataset / download script
   * training & evaluation commands
   * results (+ seeds)
5. Use `pre-commit run --all-files` before pushing; it formats code & markdown.

---

## 📝 License

MIT — see `LICENSE` for details.

---

Happy hacking & reproducing! 🎉
