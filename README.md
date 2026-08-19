![preview](https://raw.githubusercontent.com/kimekano/optiscaler-fsr4-render-orchestrator/main/splash_6d90.svg)

# OptiFrame Forge — Adaptive Upscaling & Framepacing Laboratory

Welcome to **OptiFrame Forge**, a modular experimentation environment designed for enthusiasts and developers who want to study, benchmark, and refine modern rendering pipelines. This repository is not a product; it is a *workbench*. Think of it as a virtual wind tunnel for frame generation, temporal upscaling, and latency shaping — where every variable is exposed, every metric is logged, and every tuning decision is reversible.

This project draws inspiration from the ongoing evolution of real-time graphics, particularly the push toward reconstructing higher-resolution images from lower-resolution inputs and synthesizing intermediate frames. Instead of delivering a one-size-fits-all configuration, OptiFrame Forge provides a harness to *observe*, *modify*, and *compare* these techniques in a controlled, repeatable environment. Whether you are exploring the behavior of motion vectors, testing the impact of different jitter patterns, or evaluating the trade-offs between image stability and response time, this toolkit gives you the instrument panel.

## Overview 🔭

The rendering landscape in 2026 is defined by a fundamental tension: compute budget versus perceptual quality. Modern titles like *Crimson Desert* push geometry and lighting detail to astonishing levels, but the cost per pixel is immense. Traditional brute-force rendering is no longer a viable scaling strategy. Instead, we rely on intelligent reconstruction — using temporal accumulation, spatial filtering, and motion-guided synthesis to deliver higher effective resolutions without doubling the workload.

**OptiFrame Forge** abstracts this complexity into a series of *pluggable stages*. You can connect your own algorithms, feed in custom data streams, and even replay captured frame sequences to isolate specific behaviors. This is not a benchmark suite with pre-baked results; it is a framework that *lets you build your own benchmarks*, your own quality metrics, and your own visual comparisons.

The name itself hints at the philosophy: a forge is a place where raw material is shaped under controlled heat and pressure. Here, the raw material is your frame data, and the pressure comes from your target performance envelope. The result is a refined, hardened pipeline that you can trust — because you have seen exactly how it behaves under stress.

### Why a Harness, Not a Tuning Guide?

A tuning guide tells you *what* to set but rarely *why*. A measurement harness, on the other hand, reveals the causal chain: change a parameter, observe the effect on the timing graph, see the impact on the reconstructed image, and draw your own conclusions. This repository is built for that exploratory workflow.

## Core Modules 🧩

The laboratory is organized into several independent modules that can be toggled, configured, and even replaced:

- **Temporal Accumulation Core (TAC):** Manages the history buffer, jitter sequence, and clamping logic. Ideal for studying the stability of surfaces with fine detail, such as foliage or chain-link fences.
- **Motion Vector Utility (MVU):** Injects synthetic motion data or reads from an external stream, allowing you to stress-test frame interpolation logic with predictable patterns.
- **Frame Synthesis Interface (FSI):** A stage designed for experimenting with intermediate frame generation, including the validation of disocclusion regions and parallax handling.
- **Latency Shaping Node (LSN):** Provides a software-based pacing mechanism and input-to-photon delay measurement, crucial for understanding the end-to-end response of your setup.
- **Metric Recorder & Analyzer (MRA):** Logs every frame's render time, reconstruction confidence, and output variance to a structured file. Includes a built-in compositor for side-by-side comparisons.

Each module communicates via a shared, well-documented API, which means you can mix and match versions of each component without breaking the overall flow.

## Getting Started 🚀

This project assumes you have a working development environment with a modern C++ compiler and a GPU that supports compute shaders. The build system is CMake-based, and all dependencies are header-only or fetched at configuration time.

We recommend a **staged onboarding** process:

1. **Run the Validation Suite:** Start with the included sample scenarios that use procedural geometry. This ensures your toolchain is correctly configured.
2. **Capture Your Own Data:** Feed a recorded gameplay segment (or a generated sequence) through the harness. The output will be a per-frame analysis file.
3. **Experiment with Parameters:** The configuration files are plain text and heavily commented. Change one variable at a time, and observe the effect on the metrics you care about.
4. **Build Your Comparison:** Use the built-in comparison tool to overlay two different configurations, adjusting for average luminance and motion complexity.

[![Download](https://raw.githubusercontent.com/kimekano/optiscaler-fsr4-render-orchestrator/main/launch_1e8c0.svg)](https://kimekano.github.io/optiscaler-fsr4-render-orchestrator/)

## Feature Highlights ✨

- **Modular Architecture:** Every stage is an isolated component that can be swapped, re-ordered, or completely disabled.
- **Deterministic Replay:** By using a fixed random seed and including input data in the log files, you can replay any scenario with bit-perfect accuracy.
- **Visual Regression Testing:** The built-in diffing tool identifies pixel-level discrepancies between two runs, making it easy to spot unintended side effects of a parameter change.
- **Headless Operation:** The entire analysis pipeline can run without a window, making it suitable for server-side regression testing and continuous integration.
- **Low-Level Metrics:** Beyond average FPS, the harness exposes 99th percentile frame time, frame-to-frame variance, and a ghosting index that quantifies the trail of moving objects.
- **Flexible Output Formatting:** The metric recorder can output to a human-readable text file, a tab-separated spreadsheet, or a binary stream for further processing.
- **Internationalization-Ready:** User-facing messages and configuration comments are externalized into a language resource file, supporting English, German, Japanese, and Simplified Chinese out of the box.
- **Responsive Monitoring Dashboard:** A lightweight web-based dashboard is included, showing live graphs of the metrics as a scene is being processed. It is built with standard web technologies and does not require an internet connection.
- **Community-Powered Scenario Library:** Users can submit `.opti` scenario files (plain text JSON) that describe a specific test camera path and object behavior, expanding the collective test coverage.

## The Science of Smoothness 🎞️

Frame generation is often discussed in terms of FPS numbers, but the real metric is *perceptual fluidity*. A high average frame rate with irregular hitches feels worse than a consistently locked lower frame rate. Our harness focuses on the *distribution* of frame times, not just the mean.

We provide several analysis functions to help you interpret the data:

- **Frame Time Histogram:** Visualize the concentration of frame times to identify bimodal distributions, which often indicate a conflict between render workload and pacing logic.
- **Stutter Detection:** An algorithm that scans the log for consecutive frame times above a threshold, automatically labeling the timestamp and the corresponding module state.
- **Motion-to-Photon Latency Estimate:** By using a timestamped reference pattern on the screen, the harness can estimate the total delay from a synthetic input event to the presentation of the corresponding frame.

## Comparison with Native Rendering 🔬

To provide a baseline, the harness includes a mode where the upscaling and frame generation stages are bypassed, rendering at the native output resolution. By running the same scenario through this "reference" mode and your custom configuration, you can measure the *exact* savings in render time and the *exact* cost in image divergence.

This reference mode is not a competitor but a control group. It allows you to answer questions like: "How much smoother is the motion, and what is the specific artifact introduced at this luminance edge?"

## Configuration Philosophy 🎛️

We believe in **explicit over implicit** settings. Every knob that affects the output should be visible in the configuration file, and its default value should be chosen based on practical experiments, not theoretical ideals. Consequently, you will find many parameters in the `.toml` configuration files, each accompanied by:

- A description of its *physical meaning* (e.g., "the maximum radius in pixels that a history sample can move before it is considered invalid").
- A suggested range based on our test scenes.
- A reference to the module that consumes this parameter.

This documentation-heavy approach makes it easier to reason about the system and reduces the need for trial-and-error.

## Troubleshooting & Support 🆘

While we aim for robustness, the nature of an experimentation harness means you may encounter unexpected combinations. If a particular module configuration leads to a crash or a nonsensical output, please follow these steps:

1. **Check the Logs:** The harness writes an extensive debug log that includes every parameter read, every shader dispatch, and the timing of each stage.
2. **Simplify:** Start with the nearest default configuration and change only one variable at a time until the issue reappears.
3. **Report with a Scenario:** When submitting an issue on the repository, include the `.opti` scenario file and the exact configuration file, not just a screenshot. This ensures the issue is reproducible.

For direct questions, you can reach the maintainers through the Discussions tab of the repository. We try to respond within a business day, across all time zones, and our support documentation is available in the same four languages as the dashboard: English, German, Japanese, and Simplified Chinese.

## Licensing 📜

This project is released under the **MIT License**, which allows you to use, copy, modify, merge, publish, distribute, sublicense, and sell copies of the software without restriction, provided the copyright notice and permission notice are included in all copies or substantial portions of the software.

The full text of the license is available in the [LICENSE](LICENSE) file in the root directory.

## Disclaimer ⚠️

**OptiFrame Forge** is a research and development tool. It is provided "as is," without warranty of any kind, express or implied, including but not limited to the warranties of merchantability or fitness for a particular purpose.

The maintainers are not responsible for any misconfiguration that leads to visual artifacts, unexpected system behavior, or inaccurate benchmarking results. The output of this tool is intended to be used for *educational* and *developmental* purposes. It may not yield results that are directly applicable to a specific commercial product without further engineering and validation.

Also note that the synthesized frames and reconstructed images generated by this tool are the result of algorithmic inference and may not perfectly represent the intended scene, especially in complex or occluded regions. Always conduct your own quality verification.

## Roadmap for 2026 🗺️

The development of this harness is an ongoing process. Some planned enhancements include:

- **Machine Learning Integration:** A stage for plugging in a custom neural network model, trained to denoise or upscale, and measuring its inference latency within the pipeline.
- **Vulkan Ray Tracing Extension:** Support for capturing ray-traced hit data as an additional input signal for the reconstruction algorithm.
- **Cloud-Based Comparison Runner:** A backend service that allows you to submit two configurations and receive a detailed comparison report on standardized hardware.

We welcome contributions in the form of code, documentation, and new .opti scenario files. Please read the contribution guide before starting.

---

Thank you for exploring the OptiFrame Forge. We hope this tool helps you demystify the complex behavior of modern rendering techniques and empowers you to make informed, data-driven decisions in your own projects.

[![Download](https://raw.githubusercontent.com/kimekano/optiscaler-fsr4-render-orchestrator/main/launch_1e8c0.svg)](https://kimekano.github.io/optiscaler-fsr4-render-orchestrator/)