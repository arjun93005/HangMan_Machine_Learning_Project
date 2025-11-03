# 🎮 Hangman AI Agent

A sophisticated AI agent that plays Hangman using a hybrid approach combining **Hidden Markov Models (HMM)** and **Q-Learning Reinforcement Learning**.

![Python](https://img.shields.io/badge/Python-3.10.19-blue?logo=python&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-2.1.2-013243?logo=numpy&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-2.5.1%2Bcu121-EE4C2C?logo=pytorch&logoColor=white)
![JupyterCore](https://img.shields.io/badge/Jupyter_Core-5.9.1-F37626?logo=jupyter&logoColor=white)

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [How It Works](#how-it-works)
- [File Structure](#file-structure)
- [Configuration](#configuration)
- [Results and Metrics](#results-and-metrics)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

---

## 🎯 Overview

This project implements an intelligent Hangman-playing agent that learns to guess letters optimally by:
- **Analyzing language patterns** using position-specific Hidden Markov Models
- **Learning from experience** through Q-Learning reinforcement learning
- **Combining both approaches** for robust decision-making

The agent trains on a custom word corpus and evaluates its performance on test games, providing detailed metrics and visualizations.

---

## ✨ Features

- **Hybrid AI Architecture**: Combines probabilistic modeling (HMM) with reinforcement learning (Q-Learning)
- **Corpus-Based Training**: Learns language patterns from your custom word list
- **Multiple Word Lengths**: Trains separate HMMs for each word length
- **Experience Replay**: Efficient learning through batch updates
- **Comprehensive Metrics**: Tracks wins, wrong guesses, repeated guesses, and overall score
- **Training Visualization**: Plots reward and success rate progression
- **Flexible Evaluation**: Test on specific words or random samples

---

## 🚀 Installation

### Prerequisites

- Python 3.7 or higher
- pip package manager

### Install Dependencies

```bash
pip install numpy matplotlib
```

### Required Files

You need two text files in the same directory as the script:

1. **corpus.txt** (required): Training vocabulary
   - One word per line
   - Lowercase letters only
   - Example:
     ```
     apple
     banana
     cherry
     ```

2. **test.txt** (optional): Evaluation vocabulary
   - Same format as corpus.txt
   - If not provided, uses random words from corpus

---

## 🎮 Quick Start

### Basic Usage

```bash
python hangman_agent.py
```

The script will automatically:
1. Load and validate the corpus
2. Train HMMs for each word length
3. Train the Q-Learning agent (2,000 episodes)
4. Evaluate performance (2,000 games)
5. Display results and training plots

### Expected Output

```
============================================================
EVALUATION RESULTS
============================================================
Games Played: 2000
Wins: 769
Success Rate: 38.45%
Total Wrong Guesses: 9891
Total Repeated Guesses: 0

Score Components:
  Win Score (100 per win):    + 76,900
  Wrong Guess Penalty (×2):   - 19,782
  Repeated Guess Penalty (×1):- 0

🎯 FINAL SCORE: 57,118.00
============================================================
```

---

## 🧠 How It Works

### 1. Hidden Markov Model (HMM)

The HMM component models words as sequences of letters with position-specific transitions:

- **States**: 26 lowercase letters
- **Training**: Learns letter distributions and transitions from corpus
- **Inference**: Uses forward-backward algorithm to compute letter probabilities
- **Output**: Probability distribution over unguessed letters

**Example**: For the masked word `_pp__` with guessed letters `{a, e, i}`:
```
Letter Probabilities:
l: 0.35  (likely: "apple")
o: 0.25  (possible: "oppor")
u: 0.15  (less likely)
...
```

### 2. Q-Learning Agent

The Q-Learning component learns optimal action selection through experience:

- **State**: (masked_word, guessed_letters, lives_remaining)
- **Action**: Choose a letter to guess
- **Reward**: +100 for win, +10 per correct letter, -5 for wrong guess, -100 for loss
- **Policy**: ε-greedy with epsilon decay
- **Learning**: Batch updates from experience replay buffer

### 3. Hybrid Decision Making

The agent combines both approaches:

```python
Score(letter) = 0.7 × HMM_probability(letter) + 0.3 × Q_value(letter)
```

This allows:
- HMM to provide strong linguistic intuition
- Q-Learning to adapt to specific patterns
- Balanced exploration and exploitation

---

## 📁 File Structure

```
hangman-ai-agent/
│
├── hangman_agent.py      # Main script
├── corpus.txt            # Training vocabulary (required)
├── test.txt              # Evaluation vocabulary (optional)
├── README.md             # This file
└── requirements.txt      # Python dependencies
```

---

## ⚙️ Configuration

### Training Parameters

Modify these constants in the script to adjust behavior:

```python
# Q-Learning Hyperparameters
learning_rate = 0.001      # How quickly to update Q-values
gamma = 0.95               # Discount factor for future rewards
epsilon = 1.0              # Initial exploration rate
epsilon_decay = 0.995      # Exploration decay per episode
epsilon_min = 0.1          # Minimum exploration rate

# Training Settings
training_episodes = 2000   # Number of training games
evaluation_games = 2000    # Number of test games
batch_size = 32            # Replay buffer batch size
memory_size = 10000        # Max replay buffer size

# Hybrid Weights
hmm_weight = 0.7          # Weight for HMM probabilities
q_weight = 0.3            # Weight for Q-values

# Game Settings
max_wrong = 6             # Maximum incorrect guesses allowed
```

### Scoring System

Adjust penalty weights in the `evaluate_agent()` function:

```python
penalty_wrong = total_wrong * 2      # Penalty per wrong guess
penalty_repeat = total_repeated * 1  # Penalty per repeated guess
```

---

## 📊 Results and Metrics

### Evaluation Metrics

- **Success Rate**: Percentage of games won
- **Total Wrong Guesses**: Sum of incorrect attempts across all games
- **Total Repeated Guesses**: Sum of redundant guesses
- **Final Score**: Composite metric balancing wins and efficiency

### Training Plots

The script generates two plots:

1. **Reward Progression**: Moving average of episode rewards (window: 100)
2. **Win Rate Progression**: Moving average of success rate (window: 100)

These plots help visualize:
- Learning progress over time
- Convergence behavior
- Training stability

---

## 🔧 Troubleshooting

### Common Issues

**Issue**: `FileNotFoundError: 'corpus.txt' not found`
```
Solution: Create corpus.txt with one word per line in the same directory
```

**Issue**: Poor performance / Low success rate
```
Solutions:
- Ensure corpus.txt has sufficient vocabulary (1000+ words recommended)
- Increase training episodes (e.g., 5000)
- Adjust hmm_weight / q_weight ratio
- Check that corpus words are lowercase and alphabetic only
```

**Issue**: Training takes too long
```
Solutions:
- Reduce training_episodes (e.g., 1000)
- Reduce evaluation_games (e.g., 500)
- Use smaller corpus
```

**Issue**: Q-table grows too large / Out of memory
```
Solutions:
- Limit word length range in corpus
- Reduce memory_size (replay buffer)
- Consider implementing Deep Q-Networks (DQN) instead of tabular Q-Learning
```

---

**Happy Hangman Playing! 🎮🤖**
