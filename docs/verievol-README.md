<h1 align="center">
<br>
VeriEvol: Scaling Multimodal Mathematical Reasoning via Verifiable Evol-Instruct
</h1>

<div align="center">

![](https://img.shields.io/badge/Task-Multimodal%20Mathematical%20Reasoning-orange)
![](https://img.shields.io/badge/Framework-Verifiable%20Evol--Instruct-purple)
![](https://img.shields.io/badge/Data-Verified%20RL-blue)
![](https://img.shields.io/badge/Verifier-HTV--Agent-green)

</div>

<p align="center">
  <a href="https://robertmarton.github.io/verievol/"><b>[Website]</b></a> •
  <a href="./docs/static/verievol-paper.pdf"><b>[Paper]</b></a> •
  <a href="https://github.com/RobertMarton/verievol"><b>[GitHub]</b></a> •
  <a href="#release-plan"><b>[Release Plan]</b></a> •
  <a href="#citation"><b>[BibTeX]</b></a>
</p>

<p align="center">
Repo for "<a href="./docs/static/verievol-paper.pdf" target="_blank">VeriEvol: Scaling Multimodal Mathematical Reasoning via Verifiable Evol-Instruct</a>"
</p>

<br>

<p align="center">
    <img src="./docs/static/images/teaser-results.png" width="1000">
    <br>
    <em>
    Figure 1: External comparison and data-volume scaling. VeriEvol is competitive with strong 7B visual-math baselines, while evolved SFT data and verified RL data both scale upward with data volume.
    </em>
</p>

## News

- [2026/06] Project page prepared with paper, framework figure, scaling plots, main results, and citation.
- [2026/06] VeriEvol paper package prepared for public preprint release.
- Coming soon: prompts, data, model checkpoints, code, and full verifier traces will be attached when public URLs are available.

## Introduction

<div style="text-align: justify;">

Scaling reinforcement learning for visual mathematical reasoning requires more than generating harder questions: as data volume grows, the reward labels themselves must remain reliable. Existing data pipelines scale supervision while trusting the labeller, and policy-side methods assume the underlying answers are already correct.

VeriEvol treats scaling as a verifiable data-construction problem and decouples two axes before any policy update: <b>prompt difficulty</b>, expanded by route-specific evolution operators, and <b>answer reliability</b>, enforced by offline hypothesis-test falsification.

We instantiate this as <b>VeriEvol</b>, an iterative framework with two extensible components: a type-aware evolution module that rewrites low-difficulty image-question seeds into harder, image-grounded prompts; and <b>HTV-Agent</b>, a verifier that accepts an answer only after multi-source counter-evidence has failed to refute it. The resulting verified data scales in volume, extends by adding evolution routes or verifier channels, and plugs directly into existing GRPO-style RL recipes.

</div>

<br>

<p align="center">
    <img src="./docs/static/images/framework.png" width="1000">
    <br>
    <em>
    Figure 2: VeriEvol framework. Prompt Difficulty Control generates harder image-grounded prompts; Answer Reliability Control verifies candidate answers with HTV-Agent; Closed-loop Refinement feeds accepted samples into SFT, RL, curriculum prioritization, and seed-pool refresh.
    </em>
</p>

## Method

VeriEvol is organized around a simple scaling principle: separate the components that should grow along different axes.

- <b>Prompt Difficulty Control.</b> Low-difficulty image-question seeds are routed by task type into route-specific evolution operators, including decomposition, rephrasing, constraint strengthening, multi-hop reasoning, inverse solving, and visual grounding.
- <b>Answer Reliability Control.</b> HTV-Agent screens candidate answers through multiple solver hypotheses, refutation-seeking verification, programmatic checks, visual checks, conflict-aware decision, and a deterministic acceptance gate.
- <b>Closed-loop Refinement.</b> Accepted samples form VeriEvol-SFT and VeriEvol-RL pools. Rollout-based difficulty estimates are used for curriculum prioritization and seed-pool refresh.

<p align="center">
    <img src="./docs/static/images/training-dynamics.png" width="560">
    <br>
    <em>
    Figure 3: GRPO training dynamics on original vs. evolved prompts. Evolved prompts reach a higher terminal reward while sustaining higher policy entropy.
    </em>
</p>

<p align="center">
    <img src="./docs/static/images/htv-ablation.png" width="900">
    <br>
    <em>
    Figure 4: HTV-Agent component ablation. The full verifier improves raw single-call accuracy with complementary gains from self-consistency voting and programmatic checks.
    </em>
</p>

## Experiments

All internal rows share the Qwen2.5-VL-7B-Instruct backbone and evaluate on five held-out visual mathematical reasoning benchmarks.

<table border="1" cellspacing="0" cellpadding="5" style="border-collapse:collapse; text-align:center;">
  <thead>
    <tr>
      <th>Method</th>
      <th>MathVista<br>Mini</th>
      <th>MathVision<br>Mini</th>
      <th>MathVerse<br>VO</th>
      <th>DynaMath<br>Worst</th>
      <th>We-Math<br>Strict</th>
      <th>Avg.</th>
    </tr>
  </thead>
  <tbody>
    <tr><th colspan="7" style="text-align:center;"><b><i>External 7B-size baselines</i></b></th></tr>
    <tr><td>OpenMMReasoner-7B</td><td><b>79.50</b></td><td>43.60</td><td>63.80</td><td>34.90</td><td>53.81</td><td>55.12</td></tr>
    <tr><td>ReVisual-R1-7B</td><td>73.10</td><td><b>48.80</b></td><td>53.60</td><td>27.50</td><td>42.00</td><td>49.00</td></tr>
    <tr><td>OVR-7B</td><td>72.10</td><td>38.20</td><td>54.60</td><td>33.50</td><td>44.60</td><td>48.60</td></tr>
    <tr><td>MMR1-Math-v0</td><td>72.00</td><td>29.00</td><td>55.40</td><td>27.90</td><td>31.90</td><td>43.24</td></tr>
    <tr><td>WeThink-7B</td><td>70.90</td><td>27.20</td><td>44.70</td><td>24.40</td><td>48.00</td><td>43.04</td></tr>
    <tr><td>VLAA-Thinker-7B</td><td>68.00</td><td>26.40</td><td>48.20</td><td>22.40</td><td>41.50</td><td>41.30</td></tr>
    <tr><td>VL-Rethinker-7B</td><td>73.70</td><td>28.40</td><td>46.40</td><td>17.80</td><td>36.30</td><td>40.52</td></tr>
    <tr><td>ThinkLite-VL-7B</td><td>71.60</td><td>24.60</td><td>42.90</td><td>16.50</td><td>41.80</td><td>39.48</td></tr>
    <tr><td>MM-Eureka-Qwen-7B</td><td>72.60</td><td>32.10</td><td>45.40</td><td>23.00</td><td>21.80</td><td>38.98</td></tr>
    <tr><td>InternVL3-8B</td><td>70.50</td><td>28.60</td><td>33.90</td><td>23.00</td><td>37.50</td><td>38.70</td></tr>
    <tr><td>Qwen2.5-VL-7B-Instruct</td><td>69.20</td><td>21.80</td><td>34.10</td><td>18.00</td><td>32.30</td><td>35.08</td></tr>
    <tr><th colspan="7" style="text-align:center;"><b><i>Supervised Fine-Tuning (ours)</i></b></th></tr>
    <tr><td>Seed-only SFT</td><td>74.10</td><td>36.16</td><td>65.02</td><td>26.96</td><td>56.76</td><td>51.80</td></tr>
    <tr><td>VeriEvol-SFT</td><td>76.60</td><td>39.80</td><td>67.01</td><td>30.94</td><td>59.33</td><td>54.73</td></tr>
    <tr><th colspan="7" style="text-align:center;"><b><i>Reinforcement Learning (ours)</i></b></th></tr>
    <tr><td>RL-Origin</td><td>77.00</td><td>41.45</td><td>69.04</td><td>30.54</td><td>58.19</td><td>55.24</td></tr>
    <tr><td>RL-Evol</td><td>78.10</td><td>45.39</td><td>69.67</td><td>32.14</td><td>60.00</td><td>57.06</td></tr>
    <tr><td><b>RL-Evol + Verifier (full VeriEvol)</b></td><td><b>79.00</b></td><td><b>47.37</b></td><td><b>70.46</b></td><td><b>35.73</b></td><td><b>63.05</b></td><td><b>59.12</b></td></tr>
  </tbody>
</table>

Full VeriEvol adds <b>+3.88</b> average points over RL-Origin, including <b>+1.82</b> from evolved prompts and <b>+2.06</b> from HTV-Agent verification.

## Scaling Analysis

- Scaling evolved SFT data from 10K to 250K samples raises the five-benchmark mean from <b>35.42</b> to <b>54.73</b>.
- Scaling verified RL data from 10K to 130K samples raises the mean from <b>54.52</b> to <b>59.12</b>.
- HTV-Agent improves the six-benchmark inference-time judging mean by <b>+4.51pp</b> over raw single-call judging.
- Full VeriEvol improves MathVision-Mini by <b>+5.92</b> over RL-Origin.

## Release Plan

The paper describes the release of the following artifacts:

- prompts
- training data
- model checkpoints
- code
- full verifier traces for accepted samples

Public URLs will be added here when available.

## Website

The project website is stored in `docs/` and served through GitHub Pages.

```text
https://robertmarton.github.io/verievol/
```

To preview locally:

```bash
cd docs
python3 -m http.server 8000
```

Then open:

```text
http://127.0.0.1:8000/
```

## Citation

If you find this work helpful, please consider citing:

```bibtex
@article{li2026verievol,
  title={VeriEvol: Scaling Multimodal Mathematical Reasoning via Verifiable Evol-Instruct},
  author={Li, Haoling and Zheng, Kai and Wu, Jie and Xu, Can and Sun, Qingfeng and Hu, Han and Yang, Yujiu},
  year={2026}
}
```

## Acknowledgement

We thank the open-source multimodal reasoning and RLVR communities. VeriEvol is designed to be compatible with existing GRPO-style reinforcement learning recipes and to make multimodal data construction auditable through explicit verifier traces.

## License

Please refer to the paper and artifact release terms. Code, data, model checkpoints, and verifier traces may be released under separate licenses.
