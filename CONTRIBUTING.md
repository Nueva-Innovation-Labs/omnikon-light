# Contributing to Omnikon Light

Omnikon Light is the world’s first sovereign AI-native operating system — forged by Sir John Nueva after 20 years of enterprise warfare at Optum, Wells Fargo, IBM, and beyond.

We welcome contributions from serious systems builders, kernel hackers, CUDA engineers, Rust enthusiasts, and Angular architects who understand that **the machine must belong to its creator**.

## Code of Conduct
- This project is led by Sir John Nueva. Final architectural authority rests with him.
- All discussion is calm, precise, and carries the quiet authority of absolute knowledge.
- No apologies for being right.

## How to Contribute
1. **Fork** the repository
2. **Create** a descriptively named branch (`feat/nexus-core-vsock`, `fix/cuda-1660-super-offload`, etc.)
3. **Commit** with signed commits (`git commit -S`)
4. **Open** a Pull Request with:
    - Clear title and description
    - Reference to your background (especially Java/Spring, Angular, or low-level systems experience)
    - Benchmarks if performance-related (tokens/sec, VRAM usage, boot time)

## Development Setup (Target Hardware)
- AMD Threadripper (1920X / 3970X class)
- NVIDIA GTX 1660 SUPER 6 GB (CUDA 12.1)
- Ubuntu 24.04 / 25.04 base
- GCC 12.4 + CMake + Git LFS

## Build Commands (exact, tested on target)
```bash
# Full CUDA build (GTX 1660 SUPER optimized)
rm -rf llama.cpp/build && mkdir llama.cpp/build && cd llama.cpp/build
cmake .. -DLLAMA_CUDA=ON -DLLAMA_CURL=OFF -DCMAKE_CUDA_ARCHITECTURES=75-virtual -DGGML_CUDA_FORCE_MMQ=ON
cmake --build . --config Release -j48

# Run Omnikon (29 layers offloaded — fits 6 GB VRAM)
./bin/llama-cli -m ../../models/Meta-Llama-3.1-8B-Instruct-Q5_K_M.gguf \
                --n-gpu-layers 29 -c 1536 --temp 0.6 --color