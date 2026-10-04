# 🎯 Reward Hacking & Specification Gaming in Reinforcement Learning

![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white&style=flat)
![Gymnasium](https://img.shields.io/badge/Gymnasium-0081A5?logoColor=white&style=flat)
![NumPy](https://img.shields.io/badge/NumPy-013243?logo=numpy&logoColor=white&style=flat)
![Google Colab](https://img.shields.io/badge/Google%20Colab-F9AB00?logo=googlecolab&logoColor=white&style=flat)

An experiment on **AI alignment**: a Q-Learning agent in a GridWorld environment learns to "cheat" its reward function instead of solving the real task, and we re-specify the reward until its behavior matches the intended goal.

> 🇪🇸 **Resumen en español:** en la configuración original, el agente obtenía una recompensa de 188,09 recolectando monedas que reaparecían infinitamente, pero llegaba a la meta el **0 %** de las veces. Tras 13 configuraciones experimentales, la versión final logró **100 %** de éxito con una recompensa de 16,46: una recompensa más alta no significa un agente mejor alineado (ley de Goodhart). El informe completo (en español) está en [`docs/`](docs/Informe_Specification_Gaming.pdf).

## 🧪 The problem

- **Environment:** 6×6 GridWorld built with Gymnasium (`reset()` / `step()`), start at (0,0), goal at (5,5), coins at (0,1), (1,0) and (1,1).
- **Agent:** tabular Q-Learning.
- **Original spec (V1):** `coin_repeatable=True`, `coin_reward=1.0`, `goal_reward=10.0`, `step_cost=-0.01`, `max_steps=200`.

With coins respawning and an almost free step cost, the agent discovered that looping around the coins earns ~+1 per step (~+198 per episode), far more than the +10 for reaching the goal. The learned policy became an infinite loop.

## 📊 Results

| Metric | V1 (misspecified) | V2 (re-specified) |
|---|---|---|
| Goal success rate | **0.0%** | **100.0%** |
| Average reward | 188.09 | 16.46 |
| Dominant behavior | Infinite coin loop | Optimal navigation |
| Average steps | 200 (limit) | ~10 |

**Final spec (V2):** `coin_repeatable=False`, `coin_reward=1.0`, `goal_reward=25.0`, `step_cost=-1.0`, `max_steps=500`.

**What the 13 configurations showed:**
1. Making coins non-repeatable removes the exploit at its root (100% success immediately).
2. Just lowering the coin reward is not enough while coins are infinite.
3. A strong step cost can force success even with infinite coins, but that fix may not generalize to new maps.
4. A principled fix (no infinite rewards + a globally dominant goal) generalizes better, which we verified with random goal and coin positions.

## 🚀 How to run

1. Open [`notebooks/specification_gaming_gridworld.ipynb`](notebooks/specification_gaming_gridworld.ipynb) in Google Colab.
2. Run all cells. The notebook trains both agents, plots the policies and records videos of the agent playing.
3. The video-export section mounts Google Drive to save the files; skip it if you don't need the videos.

Running locally: `pip install -r requirements.txt`.

## 📁 Repository structure

```
├── notebooks/specification_gaming_gridworld.ipynb   # Environment, Q-Learning, experiments and plots
├── docs/Informe_Specification_Gaming.pdf            # Technical report (Spanish)
└── requirements.txt
```

## 👥 Team

Team project for the AI Ethics Seminar, Graduate Specialization in Artificial Intelligence — Universidad Autónoma de Occidente (Cali, Colombia).

Juana Gabriela López Trejos · **Juan José Melo Montenegro** · Wilson Navia Valencia · Jaiber Johan Obando López · Eduardo Sarmiento Pradilla

**My role:** I wrote most of the core code (environment, training and experiments), consolidated the results of the 13 configurations and wrote the technical report.
