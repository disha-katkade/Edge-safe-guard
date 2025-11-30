# **Edge Safe Guard⛑️**

***A lightweight, vision-based safety compliance model built for edge devices using Edge Impulse Studio.***
| Model Development | Edge Safe Guard |
| :------- | :------: | 

Edge Safe Guard is a lightweight computer-vision model designed to detect helmet compliance in industrial environments using Edge Impulse Studio.
The primary goal is to build a model that is accurate, energy-efficient, and deployable on edge devices where compute and memory budgets are limited.

This repository contains the complete model-development workflow: dataset curation, feature exploration, model training, optimization, and deployment artifacts.

## **Overview**

Industrial sites continue to struggle with monitoring compliance for essential safety gear. Many existing solutions require cloud connectivity, high compute resources, or large models unsuitable for edge deployment.

**Edge Safe Guard** addresses this by designing a small, fast, edge-ready model capable of identifying helmet vs. no-helmet in real-world conditions.
This project is developed using Edge Impulse with strong emphasis on:
- High-quality dataset handling
- Iterative model optimization
- Efficient on-device performance
- Edge-ready deployment using WebAssembly (Browser SIMD)

The current prototype achieves ~40% accuracy due to dataset imbalance and limited helmet samples, but the pipeline is intentionally engineered to allow rapid dataset scaling and retraining.

## **🏗️Dataset**

| Feature | Details |
|---------|---------|
| **📂 Dataset Source** | `Helmet Safety` — COCO Format *(Open-source)* |
| **🖼️ Total Images** | `~11,000` |
| **🏷️ Annotations** | Helmet / No-Helmet |
| **⚙️ Preprocessing** | Resizing → Normalization → COCO → Edge Impulse ingestion |
| **📊 Split Ratio** | `80% Train / 20% Validation` |
| **✨ Augmentations** | Flip 🔄 | Rotate 🔁 | Color Jitter 🎨 | Brightness ±20% 🌞 |

Care was taken to maintain image variety:
- different lighting conditions
- multiple worker postures
- front/side profiles
- partially occluded helmets

This variability was critical for generalization but also introduced class imbalance, reflected in the model accuracy.

## Model Architecture (Edge Impulse Studio)

| Feature | Details |
|---------|---------|
| **🖼️ Feature Block** | Image (`96x96`, Grayscale & RGB tested) |
| **⚙️ Training Block** | Transfer Learning — `MobileNetV2-0.35` |
| **🔧 Optimization** | Quantized `INT8` |
| **🚀 Deployment** | WebAssembly (Browser SIMD) |

Why MobileNetV2-0.35?
- small footprint
- well-suited for embedded CPUs
- good balance of inference speed and accuracy
- compatible with Edge Impulse quantization pipeline

## 🏗️ Model Selection & Experiments

### Why **MobileNetV2-0.35**?
- 🟢 Small footprint — ideal for lightweight deployment  
- ⚡ Well-suited for embedded CPUs  
- ⚖️ Good balance of inference speed and accuracy  
- 🛠️ Compatible with Edge Impulse quantization pipeline  

---

## **🔬 Experiments & Iterations**

A structured iterative cycle was followed to refine the model:

#### ✔ Experiment Set A – Baseline
- **Input:** 96×96 RGB  
- **Augmentation:** None  
- **Accuracy:** ~29–34%  

#### ✔ Experiment Set B – Augmentation + Balanced Sampling
- Heavy augmentation applied  
- Stratified sampling to balance classes  
- **Accuracy improved to:** ~40%  

#### ✔ Experiment Set C – Model Variants

| Model Variant       | Notes                    | Outcome       |
|--------------------|--------------------------|--------------|
| MobileNetV2-0.35    | Good speed, low compute  | Best stability |
| MobileNetV1         | Overfit quickly          | Rejected     |
| EfficientLite       | Slow on browser WASM     | Rejected     |

#### ✔ Final Optimized Model
- **Input:** 96×96  
- **Quantization:** INT8  
- **Latency (Browser SIMD):** ~27ms  
- **Accuracy:** 40.1%  

> ⚠️ Note: Due to dataset noise and class similarity (caps, hats, head angle), accuracy hit an upper limit despite augmentation.  
> The objective of this track was **model clarity and documentation**, not only accuracy.

## 🚀 Deployment (WebAssembly – Browser SIMD)

### Deployment Target
- **Platform:** WebAssembly (Browser SIMD)  
- **Reason:** No Python SDK on Windows; requires a portable, no-install runtime  

### Advantages of the WebAssembly Package
- 🖥️ Direct inference inside a browser  
- ⚡ No GPU/driver dependencies  
- 🔄 Reproducible real-time testing  

### Included Files in This Repo

- /deployment/
  - `edge-impulse-standalone.js`
  - `model.eim`
  - `wasm build files`


### Minimal Testing Interface
- Path: `/realtime-test/index.html`  
- Features: Streams webcam frames and performs inference using the WASM model  

---

## ⚠️ Limitations
- Dataset noise affects model generalization  
- Low helmet visibility in side-angle images  
- Browser-only inference is sensitive to device performance (cloud-free requirement)  
- Bounding-box detection is not included; this is a **binary classifier**, not an object detector  

---

## 🌟 Future Improvements
- Replace classifier with **FOMO-based object detection**  
- Collect a **custom dataset** with consistent lighting and framing  
- Add **segmentation masks** for better helmet boundary detection  
- Improve **recall** using synthetic data generation  
- Deploy on an **Edge AI dev board** (ESP32, Raspberry Pi, Himax)

## Repository Structure
- Edge-safe-guard/
  - `dataset/`
    - `raw/`
    - `processed/`
    - `metadata/`
  - `results/`
    - `logs, trials, confusion matrices`
  - `deployment/`
    - `webassembly-browser-simd/`
  - `README.md`

## **Edge Impulse Public Project**

A detailed description, dataset insights, experiments, and deployed model are available here:

🔗https://studio.edgeimpulse.com/public/793380/live

🔗Demo - https://youtu.be/l22_fo1F1R4

#**License**

- This project uses publicly available datasets under permissive open-source licensing.
- All code and documentation in this repository are released under the MIT License.
