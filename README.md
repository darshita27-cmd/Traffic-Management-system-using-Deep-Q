# 🚦 Autonomous Traffic Signal Control Using YOLOv8 & DQN with SUMO

This project integrates **YOLOv8** for vehicle detection, a custom **Reinforcement Learning (RL)** environment using **OpenAI Gym** and **SUMO (Simulation of Urban MObility)**, and a **Deep Q-Network (DQN)** for learning optimal traffic signal strategies. The system is trained to dynamically manage traffic light timing based on live vehicle detection and traffic parameters.

## 🧠 Key Features

* **YOLOv8-based Vehicle Detection**

  * Pretrained YOLOv8 model fine-tuned to detect **10 types of vehicles**:
    `bicycle, bike, bus, car, cng, e_rickshaw, horse_cart, tractor, truck, wheelbarrow`
  * Data Augmentation techniques used during training:
    `noise`, `blur`, and `rotation`.

* **Custom SUMO Environment**

  * Simulates a 4x4 intersection using SUMO and `sumo_rl`.
  * Integrates with a custom `gym.Env` to simulate traffic control decisions.

* **DQN (Deep Q-Network)**

  * Learns optimal signal timing based on:

    * Traffic density
    * Vehicle waiting time
    * Emergency vehicle presence
    * Vehicle type priorities and speeds
  * Rewards smooth traffic flow and penalizes delays or emergency vehicle blockage.

---

## 🧰 Installation & Setup

```bash
# Install core dependencies
!pip install tensorflow gym[all] sumo_rl

# Remove any conflicting traci versions
!pip uninstall -y traci

# Install SUMO and SUMO tools
!apt update
!apt install -y sumo sumo-tools sumo-doc
```

### ✅ Verify SUMO Installation

```bash
!sumo --version
```

---

## 📁 Project Structure

```
.
├── yolov8_vehicle_detection/
│   ├── dataset/
│   ├── train.py
│   └── yolov8_custom.pt
├── sumo_files/
│   └── 4x4.net.xml, .rou.xml, .sumocfg (SUMO config files)
├── traffic_env.py   # Custom Gym Environment
├── dqn_agent.py     # DQN model and training
└── main.py          # Training loop
```

---

## ⚙️ SUMO + traci Setup

```python
import os
os.environ['SUMO_HOME'] = '/usr/share/sumo'

import sys
if 'SUMO_HOME' in os.environ:
    tools = os.path.join(os.environ['SUMO_HOME'], 'tools')
    sys.path.append(tools)
import traci
```

---

## 🎮 RL Environment: `TrafficEnv`

Custom `gym.Env` simulates real-time signal control:

* **Observation Space**: `[traffic_density, wait_time, emergency_vehicle, vehicle_counts...]`
* **Action Space**:
  `0 = Keep current light duration`
  `1 = Increase green light duration`
  `2 = Decrease green light duration`
* **Rewards**:

  * For smooth flow and emergency handling
    − For high wait time and congestion

---

## 🧠 DQN Agent

```python
model = Sequential([
    Dense(24, input_dim=state_size, activation='relu'),
    Dense(24, activation='relu'),
    Dense(action_size, activation='linear')
])
model.compile(loss='mse', optimizer=Adam(learning_rate=0.0005))
```

* Uses replay memory (`deque`) and ε-greedy exploration.
* Optimizes traffic control based on simulation feedback.

---

## 🏁 Training the Agent

1. Initialize the SUMO simulation and `TrafficEnv`.
2. Train the `DQNAgent` by interacting with the environment.
3. Monitor reward evolution and convergence.

---

## 📊 Results & Improvements

* The system improved traffic flow in simulations by adapting signal durations based on live traffic state.
* Potential extensions:

  * Multi-agent RL for larger intersections.
  * Real-time deployment with live video feed + YOLOv8 inference.

---


## 📚 References

* [Ultralytics YOLOv8](https://github.com/ultralytics/ultralytics)
* [SUMO Simulator](https://www.eclipse.org/sumo/)
* [SUMO-RL](https://github.com/LucasAlegre/sumo-rl)
* [OpenAI Gym](https://github.com/openai/gym)

---

## 👨‍💻 Authors

* - [@darshita27-cmd](https://github.com/darshita27-cmd) – Reinforcement Learning Setup, DQN Integration, Reward Logic, Real-Time Control
* Collaborators
* - [@ChaitanyaSekra](https://github.com/ChaitanyaSekra)) Data Augmentation, YOLOv8 Model Training

  

---
