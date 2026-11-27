# Heads-Up Limit Texas Hold'em: Strategy & Game Theory Engine

*LT 8 Final Project*

A Python poker engine and simulation study, built in a Jupyter notebook. We built a full heads-up (one-on-one) Texas Hold'em game, a Monte Carlo probability engine and six AI players. We then ran a 10,000-hands-per-match tournament to answer one research question:

> **How do different kinds of decision-makers perform against each other?**

Each AI player is placed on two game-theory axes. **Rationality** asks whether it uses probabilities or rules of thumb. **Equilibrium** asks whether it plays the same way every hand or adapts to its opponent.

---

## Team

| Member | Responsible for | Notebook section |
|---|---|---|
| Augustine | Game engine (cards, deck, hand evaluator, players, betting rules), AI-vs-AI loop, Expected Value formula | 1.1 – 1.11 |
| Kazzie | Monte Carlo equity engine and its tests | 2.1 – 2.2 |
| Marc | Error convergence study, pre-flop heatmap, post-flop breakdown | 2.3 – 2.5 |
| Jada | Agent map and the three heuristic bots (Calling Station, Rock, Maniac) | 3.1 – 3.3 |
| Poleen | EV and Adaptive agents, tournament, results analysis | 3.4 – 3.12 |

Section 4 (Discussion), the charts and code readability were shared by everyone.

---

## Files

| File | What it is |
|---|---|
| `LT_8_FINAL_PROJECT_POKER.ipynb` | The project: all code, tests, charts and write-up |
| `tournament_10000_hands.pkl` | Saved tournament results, so the tournament doesn't have to be re-run (see below) |
| `Python_Concepts_Guide.md` | A guide to the less common Python tools we used (`@dataclass`, `IntEnum`, generators, and others) |
| `README.md` | This file |

---

## Requirements

- **Python 3** (developed on Python 3.13)
- **Jupyter Notebook** or **VS Code** with the Jupyter extension
- Libraries:

```bash
pip install numpy pandas matplotlib ipywidgets
```

Everything else (`random`, `itertools`, `dataclasses`, and so on) is part of Python's standard library.

---

## How to run

1. Open `LT_8_FINAL_PROJECT_POKER.ipynb`.
2. Click **Run All** (the cells must run top to bottom, because later sections use classes from earlier ones).
3. Wait a few minutes. The slowest steps are the convergence study (2.3) and the pre-flop heatmap (2.4).

**Tests:** every test in the notebook is an `assert`. If a cell finishes without an error, its tests passed.

**Tournament:** Section 3.7 loads the saved results from `tournament_10000_hands.pkl` instantly. To replay the tournament from scratch, delete that file and run again. A full run takes about **2–3 minutes**. For a quick check, lower `TOURNAMENT_HANDS` in Section 3.6.

**Reproducibility:** every random part takes a seed, so re-running gives exactly the same numbers.

### Play against a bot yourself

Run the two cells in **Appendix A** at the bottom of the notebook to get a clickable poker table: you against a bot.

- By default it uses **No-Limit** betting: you choose your bet size with a slider.
- To play the **Fixed-Limit** format used in the study, add `betting="limit"` to `GameEngine(...)`.
- To face a stronger opponent, replace `RandomAgent()` with `EVAgent(preflop_table=PREFLOP_EQUITY)` or another bot.

---

## How the notebook is organized

Every subsection follows the same pattern: **what & why → code → tests → chart → what it shows.**

### Section 1: The game engine (Basic goal)
| Part | What it does |
|---|---|
| `Card`, `Deck` | Immutable cards. A seedable deck that shuffles and deals |
| `HandEvaluator` | Finds the best 5-card hand out of 7 cards and ranks it, including kickers, the wheel (A-2-3-4-5) and ties |
| `TableState` | The read-only snapshot an AI player sees on its turn (its own cards, board, pot, legal actions). It never shows the opponent's cards |
| `Player`, agents | A seat at the table with chips and a decision-maker ("agent") |
| `GameEngine` | Runs full hands: blinds, four betting rounds, fold or showdown, pot splitting. Supports No-Limit and Fixed-Limit betting, and checks after every hand that no chips are created or lost |
| `calculate_call_ev`, `calculate_bet_ev`, `pot_odds` | The Expected Value formulas the rational agents use |

### Section 2: The probability engine (Intermediate goal)
| Part | What it does |
|---|---|
| `calculate_equity()` | Estimates how often a hand wins, ties or loses by simulating thousands of random boards (Monte Carlo) |
| `fast_hand_key()` | A faster hand scorer, about 25× faster, tested to agree with `HandEvaluator` on 50,000 random hands |
| Convergence study | Shows the simulation error shrinks like 1/√N, as theory predicts |
| Pre-flop heatmap | Win chance of all 169 starting hands, shown as a 13×13 grid |
| Post-flop breakdown | Win/tie/loss bars for typical hands on one flop |

### Section 3: AI players and the tournament (Advanced goal)
| Agent | Style |
|---|---|
| Random | Picks a random legal action (baseline) |
| Calling Station | Almost always checks or calls, never raises |
| Rock | Plays only the top ~15% of hands, then needs top pair or better |
| Maniac | Ignores its cards, bets or raises about 85% of the time |
| EV | Picks the action with the highest Expected Value |
| Adaptive | Same as EV, but learns how often each opponent folds and bluffs accordingly |

Every pair of agents plays 10,000 Fixed-Limit hands. Results are reported in **bb/100** (big blinds won per 100 hands) with standard errors.

### Section 4: Discussion and conclusion
What we found, its limits, and future work.

### Appendix A: Play it yourself
The interactive human-vs-bot table.

---

## Main results

- **Rationality wins.** The EV agent (+139 bb/100) and the Adaptive agent (+132 bb/100) beat every rule-of-thumb bot. All four heuristic bots lose money on average.
- **No heuristic style is best.** Rock beats Calling Station, Maniac beats Rock, but Maniac only breaks even against Calling Station, a rock-paper-scissors pattern.
- **Adapting has limits.** The Adaptive agent earns more than EV against bots that fold a lot, but it loses head-to-head to EV (−61 bb/100). It learns *how often* an opponent folds, not *which hands* they keep playing.

Full numbers and charts are in Sections 3.8 – 3.12.

---

## Known limitations

- Equity is calculated against a **random** opponent hand, not a realistic range of hands.
- The rational agents only consider the minimum raise size. This doesn't matter in Fixed-Limit, but it does in No-Limit.
- The Adaptive agent learns only one statistic (how often the opponent folds to a bet).
- The results only hold for this pool of six bots.

See Section 4 of the notebook for the full discussion and future work.
