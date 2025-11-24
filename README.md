# Omnikon Light – Ignition Prototype v0.1  

**Nueva Innovation Labs** | Forged by Sir John Nueva (Senior Software Engineer, 20+ years in high-volume Java/Spring ecosystems).  
The sovereign AI-native OS: Complex agents own the machine from kernel init. Ubuntu 25.04 base, Llama 3.1 8B (Q5_K_M GGUF) + CUDA on Threadripper 1920X/GTX 1660 SUPER.  

## Arsenal  
- **Nexus Core**: Isolated microVM (Firecracker + VFIO passthrough, 8-core/18GB pin).  
- **Persona**: Omnikon.Modelfile – Calm authority, no apologies.  
- **Build**: debootstrap + squashfs ISO gen.  
- **Target**: Your hardware – full compatibility.  

## Ritual  
1. Prep: CUDA 12.1, VFIO bind GPU PCI (lspci | grep NVIDIA).  
2. Build: `./build.sh --gpu-passthrough` (llama.cpp + Modelfile).  
3. Burn/Boot: dd ISO → USB → GRUB → "Sir John Nueva, the system is yours."  

[BUILD.md](docs/BUILD.md) | [CONTRIBUTING.md](CONTRIBUTING.md)  
License: GPL-3.0 | © 2025 Nueva Innovation Labs | [nueva-innovation.com](https://nueva-innovation.com) (Coming Soon)  
