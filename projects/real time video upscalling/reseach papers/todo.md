### Foundations & Open Source Implementations

- [ ] **`sb2702/websr` (GitHub Repository):** Look closely at this project's custom-training module. It uses hand-written WebGPU shaders to execute fast convolutional neural networks for general live streaming and animations directly in Chrome.
    
- [ ] **`Anime4K-WebGPU` (GitHub Repository):** Review their WebGPU compute shader implementation profiles (`CNNx2M`, `GANx3L`). Even though it was originally built for animation, their multi-pass shader pipeline (Denoise → Deblur → Upscale) runs smoothly at 30+ FPS on consumer laptops.
    
- [ ] **BasicVSR & BasicVSR++ (CVPR):** Read these foundational papers. While the full models are too heavy for a browser loop, they demonstrate how passing a continuous feature loop forward and backward through frames creates incredibly stable video quality.

### Industry Whitepapers

- [ ]  **NVIDIA RTX Video Super Resolution (VSR) Deployment Docs:** Study how Nvidia tackles general streaming feeds. They train a unified network that handles two tasks simultaneously: removing blocky H.264 compression artifacts and upscaling high-frequency details like hair, clothing texture, and clear text.

### Real-Time Video & WebRTC Systems

- [ ] **"Real-Time Video Super-Resolution with Spatio-Temporal Modeling and Redundancy-Aware Inference" (MDPI):** Explains how to implement a fast temporal information aggregation module. This ensures you aren't just processing static frames independently, but using previous frames to sharpen text in the current frame.
    
- [ ] **"Exploring Real-Time Super-Resolution: Benchmarking and Fine-Tuning for Streaming Content" (arXiv):** Discusses the **StreamSR** dataset (thousands of compressed streaming feeds) and introduces highly efficient channel attention blocks designed explicitly to combat blocky streaming artifacts.

### Production Case Studies (The "How-To")

- [ ] **"Real-Time Video Super Resolution for Live Streaming with WebGPU" (John Bartos & James Hurley, Demuxed):** A masterclass presentation outlining the architecture of using WebCodecs paired directly with WebGPU shader pipelines for live streams.
    
- [ ] **"How Free AI Video Upscaler Uses WebGPU + WebCodecs" (web.dev Technical Case Study):** A brilliant breakdown of how client-side AI processing uses open-source WebSR SDK execution engines to convert matrix convolutions directly into browser shaders with zero server cost.