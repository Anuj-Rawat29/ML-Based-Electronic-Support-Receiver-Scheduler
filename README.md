# ML-Based-Electronic-Support-Receiver-Scheduler
ML-based Electronic Support receiver scheduler that learns which frequency band to scan next, intercepting radar emitters faster than a fixed sweep.
An adaptive, learning-based scheduler that decides which frequency band an Electronic Support (ES) receiver should scan next, so it can intercept radar and communication emitters faster than a fixed sweep.

Problem

An Electronic Support receiver listens for hostile radar and communication signals, but it can only observe a small part of the radio spectrum at any moment. It must keep moving across many frequency bands.

Traditional receivers use an open-loop sweep: a fixed, pre-programmed schedule that scans every band in order. This approach:

Wastes time on empty or unimportant bands
Can miss short-lived, intermittent, or frequency-agile emitters
Cannot adapt when emitters move, change frequency, or scan periodically
Has no reliable prior intelligence to guide where to look

Challenge: build a machine-learning-based scheduler that learns from live hits and misses and decides which band to scan and when, without prior knowledge of the emitters.

Solution

We replace the blind sweep with a smart, adaptive scheduler. At every time step it chooses a band, observes the result (hit, miss, or false alarm), and updates its beliefs to make better decisions next time.

The problem is treated as a sequential decision problem under uncertainty (a restless multi-armed bandit / POMDP), where each frequency band is an "arm" and a successful intercept is the reward.

Key ideas:

Bayesian band occupancy: estimates the probability that each band is active, with forgetting so old information fades.
Exploration vs. exploitation: Thompson sampling / UCB revisits promising bands while still checking unexplored ones.
Contextual bandit / DQN: learns the value of scanning each band from recent observation history.
Periodicity detection: finds repeating emitter patterns and schedules dwells around the next predicted active window.
Frequency-agile tracking: follows emitters as they hop between bands.
Safety fallback: switches to a rule-based scan when model confidence is low.
Explainability: every decision comes with a reason and confidence score.
Solution Flow
Simulated RF Environment (emitters, bands, time slots)
            |
            v
Receiver Simulator (detection / miss / false alarm)
            |
            v
Observation Buffer (band, time, outcome, SNR, dwell)
            |
            v
Feature Engineering + Emitter State Estimator
            |
            v
ML Scheduler (Bayesian bandit / DQN / periodic module)
            |
            v
Scan Decision: next band + dwell time + explanation
            |
            v
Receiver executes scan
            |
            +----> Feedback (hit / miss / reward) ----> back to scheduler
The simulator generates emitters (static, intermittent, frequency-agile, spatially scanning, periodic).
The scheduler picks a band to scan based on its current beliefs.
The receiver model returns a detection, miss, or false alarm.
The scheduler updates its beliefs and policy from the feedback.
The evaluation module compares results against a baseline sweep.
The dashboard shows scan decisions, detections, and the comparison live.
Evaluation Metrics
Probability of detection
Probability of false alarm
Sensitivity
Average intercept rate
Average reward / cost
Prediction accuracy
Average intercept-time error

The main claim: compared with a uniform full-spectrum sweep, the adaptive scheduler improves intercept rate and reduces time-to-first-intercept.

Tech Stack
Frontend: React / Next.js, Tailwind CSS, Recharts
Backend: Python, FastAPI, WebSocket, SQLite / PostgreSQL
ML: PyTorch, Stable-Baselines3, scikit-learn, NumPy, SciPy
