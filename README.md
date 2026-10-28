# ML-Based Electronic Support Receiver Scheduler

> An adaptive scheduler that learns which frequency band an Electronic Support (ES) receiver should scan next—so it can intercept radar and communication emitters faster than a fixed sweep.

## Overview

An ES receiver can observe only a small portion of the radio spectrum at a time, so it must continually choose which frequency band to scan. A traditional open-loop sweep follows a fixed schedule, which can:

- Spend valuable time scanning empty or low-priority bands
- Miss short-lived, intermittent, or frequency-agile emitters
- Fail to adapt when emitters move, change frequency, or scan periodically
- Ignore useful information gathered from previous observations

This project explores a machine-learning-based scheduler that learns from hits, misses, and false alarms to decide **which band to scan and when**, without prior knowledge of the emitters.

## Approach

The scheduler makes a scan decision at each time step, observes the result, and updates its beliefs to improve future decisions. The problem is modeled as sequential decision-making under uncertainty—a restless multi-armed bandit / partially observable Markov decision process (POMDP)—where each frequency band is an arm and a successful intercept is the reward.

### Key capabilities

| Capability | Purpose |
| --- | --- |
| Bayesian band occupancy | Estimates the probability that each band is active, while allowing old information to fade. |
| Exploration and exploitation | Uses Thompson sampling or UCB to balance revisiting promising bands with exploring less-known ones. |
| Contextual learning | Uses recent observation history to estimate the value of scanning each band, with contextual bandit or DQN approaches. |
| Periodicity detection | Finds repeating emitter patterns and schedules scans around predicted active windows. |
| Frequency-agile tracking | Follows emitters as they hop between bands. |
| Confidence-based fallback | Switches to a rule-based scan when model confidence is low. |
| Explainable decisions | Provides a reason and confidence score for each scan decision. |

## System flow

```text
Simulated RF environment (emitters, bands, time slots)
                         |
                         v
Receiver simulator (detection, miss, or false alarm)
                         |
                         v
Observation buffer (band, time, outcome, SNR, dwell)
                         |
                         v
Feature engineering and emitter state estimation
                         |
                         v
ML scheduler (Bayesian bandit, DQN, or periodic module)
                         |
                         v
Scan decision (next band, dwell time, and explanation)
                         |
                         v
Receiver executes scan
                         |
                         +---- Feedback (hit, miss, reward) ----> Scheduler
```

In a typical evaluation loop:

1. The simulator generates emitters, including static, intermittent, frequency-agile, spatially scanning, and periodic emitters.
2. The scheduler selects a band based on its current beliefs.
3. The receiver model returns a detection, miss, or false alarm.
4. The scheduler updates its beliefs and policy from the feedback.
5. The evaluation module compares performance against a baseline sweep.
6. A dashboard displays scan decisions, detections, and comparisons.

## Evaluation

The scheduler can be evaluated using:

| Metric | What it measures |
| --- | --- |
| Probability of detection | How often active emitters are detected |
| Probability of false alarm | How often inactive bands are reported as active |
| Sensitivity | The receiver's ability to detect active emitters |
| Average intercept rate | How frequently emitters are intercepted |
| Average reward / cost | The quality of decisions under the selected reward model |
| Prediction accuracy | The accuracy of activity or state predictions |
| Average intercept-time error | The difference between actual and expected intercept time |

**Project objective:** compare the adaptive scheduler with a uniform full-spectrum sweep, aiming to improve intercept rate and reduce time to first intercept.

## Technology stack

| Area | Technologies |
| --- | --- |
| Frontend | React / Next.js, Tailwind CSS, Recharts, Plotly.js |
| Backend | Node.js, Express.js, WebSocket, PostgreSQL |
| Machine learning | PyTorch, Stable-Baselines3, scikit-learn |
| Simulation | NumPy, SciPy, Gymnasium |
