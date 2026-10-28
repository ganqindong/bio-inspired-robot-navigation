# Bio-inspired Mapping and Navigation for Robots

An archived team senior capstone project at the University of Cincinnati, exploring visual features alongside LiDAR-based boundary information in a hippocampal place-cell navigation framework.

**Team:** Qindong Gan, Wentao Zhang, and Qiyanhui Lu.  
**Faculty advisor:** Ali Minai.  
**Project period:** 2024–2025.

This is Qindong Gan's fork of the team's archived visual-extension branch. It preserves the team's source code and Git history; it is not an independently authored replacement for the team repository. The vision-LiDAR capstone experiments described in the final report were conducted in Webots. Earlier physical Create3 demonstrations belong to the preceding project and should not be attributed to this vision-extension work.

[Project description](https://ganqindong.github.io/projects/robot-mapping.html) · [Team final report](https://ganqindong.github.io/papers/robot-capstone-report.pdf) · [Original visual-extension branch](https://github.com/WentaoZhang2002/AlabiHippocampalModel/tree/senior_design_vision)

## Repository lineage

- Research foundation: Adedapo Alabi, Dieter Vanderelst, and Ali Minai, *Rapid Learning of Spatial Representations for Goal-Directed Navigation Based on a Novel Model of Hippocampal Place Fields*, Neural Networks 161 (2023), 116–128. [Paper](https://arxiv.org/abs/2206.02249).
- Earlier implementation and project work: [bduke13/AlabiHippocampalModel](https://github.com/bduke13/AlabiHippocampalModel). The team report acknowledges Andrew Gerstenslager and Bekarys Dukenbaev for the preceding work.
- Team visual extension: [WentaoZhang2002/AlabiHippocampalModel, senior_design_vision](https://github.com/WentaoZhang2002/AlabiHippocampalModel/tree/senior_design_vision), forked here at commit [c40beed](https://github.com/WentaoZhang2002/AlabiHippocampalModel/commit/c40beedfb311193831d4f28f89a289c212402421).

The inherited README also cites the authors' 2022 IJCNN paper, *Context-Dependent Spatial Representations in the Hippocampus Using Place Cell Dendritic Computation*. The current upstream repository has continued to evolve; its current default branch is not being presented as the exact original paper release.

## Project and contribution scope

The capstone explored adding an image-feature pathway to an existing navigation model with boundary-vector, head-direction, place-cell, and reward-cell components. The archived branch contains ImageNet-pretrained EfficientNetV2B0 feature extraction and an autoencoder-derived module that computes fused visual and LiDAR-based representations. The current branch does not establish that these fused representations affect place-cell activity or navigation; the integration gap below remains unresolved.

Qindong contributed to the vision pipeline, feature extraction and evaluation, literature review, and team coordination/progress reporting. The final report assigns overall navigation-model integration and fusion-layer development primarily to Wentao Zhang. Other team contributions remain part of the shared project; files are not attributed solely by which account hosts this fork.

In the archived `visual_output.py`, EfficientNetV2B0 is loaded pretrained and frozen. This should not be described as training that backbone from scratch. The separate `autoencoder.py` trains the fusion encoder. These are distinct parts of the pipeline.

The report describes limited improvements in some spatial representations and persistent perceptual aliasing in symmetric environments. This repository does not establish general navigation superiority, robust real-world deployment, or a new independent implementation of the foundational place-cell model.

## Known integration gap

In [the archived driver](https://github.com/ganqindong/bio-inspired-robot-navigation/blob/7d2e93f571dccb746c459a059f49a95e69808094/vision/visualizations/webots/controllers/create3_base/driver.py#L427-L442), the computed fused vector is assigned to `self.pcn.fusion_features` before calling `get_place_cell_activations`. However, [the corresponding place-cell implementation](https://github.com/ganqindong/bio-inspired-robot-navigation/blob/7d2e93f571dccb746c459a059f49a95e69808094/vision/visualizations/core/layers/place_cell_layer.py#L169-L180) does not read that attribute. It computes BVC activity from distances and uses those BVC activations for place-cell input.

A small isolated check of this class, with identical network state and distance inputs and learning disabled, produced identical place-cell outputs over five updates despite substantially different assigned `fusion_features` values. This check did not run Webots, evaluate saved checkpoints, or reproduce navigation experiments.

This finding concerns the currently published implementation. It does not determine which code or saved model was used for the historical report's experiments, or establish that those results are invalid. Matching historical code and run records are still needed. The archived algorithm has been preserved without wiring in a new fusion pathway or presenting a later repair as the historical implementation.

## Where to start

The default branch is `senior_design_vision`. The project is nested under `vision/`; the other branches and inherited experiments are preserved as history, not all claimed as this capstone's contributions.

| Entry | Purpose |
| --- | --- |
| [Original project README](vision/README.md) | Inherited model overview, citation, and environment notes |
| [Controller entry](vision/visualizations/webots/controllers/create3_base/create3_base.py) | Selects a robot mode and initializes the Webots driver |
| [Driver](vision/visualizations/webots/controllers/create3_base/driver.py) | Sensor and navigation controller with a visual pathway; see the integration gap above |
| [Visual features](vision/visualizations/webots/controllers/create3_base/visual_output.py) | Frozen TensorFlow/Keras EfficientNetV2B0 feature extraction |
| [Fusion](vision/visualizations/webots/controllers/create3_base/fusion_layer.py) | Combines visual and boundary-vector features using encoder weights |
| [Fusion training](vision/visualizations/webots/controllers/create3_base/autoencoder.py) | Trains the fusion encoder from stored feature vectors |
| [Neural layers](vision/visualizations/core/layers/) | Boundary-vector, head-direction, place-cell, and reward-cell implementations |
| [Webots worlds](vision/visualizations/webots/worlds/) | Includes the camera-enabled simple/cross-shaped environments |

## Environment and running scope

Clone the selected branch explicitly if needed:

```bash
git clone --branch senior_design_vision https://github.com/ganqindong/bio-inspired-robot-navigation.git
cd bio-inspired-robot-navigation
```

The team report describes Webots R2025 experiments. Inspect `vision/requirements.txt` and configure Webots to use an appropriate Python environment before selecting a world/controller. This retained requirements file is not a verified complete environment specification: the visual path additionally imports TensorFlow/Keras and OpenCV, and fusion training imports pandas. Compatible versions have not been revalidated for this fork.

This is an archived research codebase, not a one-command reproduction package. In particular:

- `controller.Supervisor` is provided by Webots, not a normal standalone Python package.
- `visual_output.py` initializes pretrained ImageNet weights on import and may download them if they are not cached.
- `fusion_layer.py` reads `encoder_weights.pth` from the working directory even though its constructor accepts a path argument.
- The controller entry currently sets a two-hour run duration and a learning mode. Review the selected mode, duration, world, working directory, and output locations before launching; this is not a short smoke command.
- Existing saved images, weights, and experimental outputs are inherited from the team branch. Their presence does not mean every figure in the report has a fully verified reconstruction path.

No training, Webots navigation run, checkpoint evaluation, or new experiment was performed while creating this fork. The two known differences in Qindong's local driver/world backup were not merged into this branch.

## Attribution and license record

The inherited [README](vision/README.md#license) labels the project MIT License with copyright 2024 University of Cincinnati. No separate full license file was found in the selected branch during this fork preparation. This fork preserves that notice and the existing authorship/history; no new license grant or sole-author ownership is asserted here. Check the inherited license record and relevant third-party terms before redistributing components separately.
