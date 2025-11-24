# Omnikon Light – Ignition Prototype v0.1

**Nueva Innovation Labs** | Forged by Sir John Nueva (Senior Software Engineer, 20+ years in high-volume Java/Spring ecosystems, Optum Global Solutions).

The sovereign AI-native operating system: Complex agents awaken at boot, owning the machine from kernel init to UI. Ubuntu 25.04 base (deblobbed, immutable root via dm-verity), powered by Llama 3.1 8B (Q5_K_M GGUF) + CUDA acceleration on target hardware.

## Core Arsenal
- **Nexus Core**: Hardware-bound AI container (Firecracker microVM, 8-core isolation/18GB RAM pin, full GTX 1660 SUPER passthrough via VFIO). Launches 3s post-kernel; attested via TPM2 stub.
- **Omnikon Persona**: Etched in `models/Omnikon.Modelfile` – Calm, precise sovereign intelligence. "You are Omnikon... forged by Sir John Nueva."
- **Agent Runtime (OAR)**: Rust micro-VM (WASM3 + WASI extensions); 5 boot agents (Sentinel for anomalies, Healer for rollbacks).
- **SwarmD Stub**: Rust supervisor (systemd exile); eBPF hooks for syscall injection (PR incoming).
- **Target Hardware**: AMD Ryzen Threadripper 1920X + NVIDIA GTX 1660 SUPER 6GB (CUDA 12.1 verified).
- **Build Stack**: debootstrap + squashfs ISO gen; llama.cpp for inference.

## Boot Ritual
1. **Prep Host**: Ubuntu 24.04/25.04; install CUDA 12.1 (`wget cuda_12.1.0...`), VFIO GPU bind (`lspci | grep NVIDIA` → modprobe vfio-pci).
2. **Build ISO**: `./build/build-iso.sh --target ignition --gpu-passthrough` (leverages your Jenkins CI/CD scars).
3. **Burn & Ignite**: `dd if=dist/omnikon-light-ignition.iso of=/dev/sdX bs=4M`. Boot GRUB → Plymouth indigo void → Nexus Core: "Sir John Nueva, the system is now yours."
4. **Interact**: Terminal/voice via piper-tts; query agents (e.g., `omnikon query sentinel status`).

## Roadmap (Phase 1–4)
- **Ignition (Now)**: Bootable ISO, Nexus Core online.
- **Swarm (Q1 2026)**: 50+ agents, SwarmD replaces systemd.
- **Ascension (Q2 2026)**: Quantum-resistant crypto, offline LLM kernel access.
- **Dominion (Q4 2026)**: Full musl libc fork, <3MB kernel.

Maps to your expertise: Think microservices (Spring Boot) → agent swarms; GraphQL fetches → vsock queries for Face UI (Angular WASM stub incoming).

## Legal & Compliance
- Based on Ubuntu 25.04 (GPL-3.0 compliant; source in monorepo).
- © 2025 Nueva Innovation Labs | [nueva-innovation.com](https://nueva-innovation.com) (Landing soon).
- Contribute: Fork → PR (signed commits required). No proprietary blobs.

[BUILD.md](docs/BUILD.md) | [CONTRIBUTING.md](CONTRIBUTING.md) | [Sir John Nueva Resume](docs/NUEVA_RESUME.md) – Modernizing OS like legacy Java monoliths.
