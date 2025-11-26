### Omnikon Light Prototype Build Procedure – Ignition Phase

# Omnikon Light — Build Instructions
**Ignition Prototype v0.1 — First Sovereign AI-Native OS**  
Forged by Sir John Nueva | Nueva Innovation Labs | 25 November 2025

## Target Hardware (Tested & Verified)
- CPU: AMD Threadripper 1920X / 3970X
- GPU: NVIDIA GTX 1660 SUPER 6 GB (CUDA 12.1)
- RAM: 64+ GB DDR4
- Storage: NVMe SSD

## Prerequisites
```bash
sudo apt update
sudo apt install build-essential cmake git curl gcc-12 g++-12 nvidia-cuda-toolkit git-lfs
git lfs install
````

#### What You Need to Work on the Prototype  
**Hardware Requirements:**  
- CPU: AMD Ryzen Threadripper 1920X (24 cores/48 threads – leverages parallel compilation for kernel and Rust builds).  
- GPU: NVIDIA GTX 1660 SUPER 6GB (for CUDA-accelerated AI inference; minimum 6GB VRAM for 8B models).  
- RAM: At least 32GB (64GB+ recommended; Nexus Core pins 20GB).  
- Storage: 500GB+ NVMe SSD (for build artifacts; NVMe is desirable for speed).  
- Internet: Stable connection (downloads ~10GB total, including model and packages).  

**Software Prerequisites (Install on Host Ubuntu):**  
- Ubuntu 24.04 LTS or 25.04 (base OS for building).  
- Essential tools: git, curl, build-essential, cmake, golang-go, rustc, cargo, ninja-build (for building llama.cpp and Firecracker).  
- Virtualization: qemu-kvm, libvirt-daemon-system, firecracker (for microVM).  
- NVIDIA stack: Proprietary drivers (version 560+), CUDA Toolkit 12.1+ (for llama.cpp acceleration).  
- Additional: debootstrap, squashfs-tools, genisoimage (for ISO creation).  
- AI Model: Llama 3.1 8B Instruct Q5_K_M GGUF (confirmed available; ~5GB download).  

**Accounts/Access:**  
- GitHub account (for repo if extending; optional for this build).  
- Root/sudo access on the host.  

**Optional Tools for Development:**  
- Cubic (GUI for ISO customization if you prefer visual over scripts).  
- TPM2-tools (for future attestation).  
- A USB drive (16GB+) for testing the ISO.  

If missing anything, install via `sudo apt update && sudo apt install [packages]`.  

#### Step-by-Step Procedure to Build the Prototype  

1. **Prepare the Host Environment (10-15 minutes)**   - completed
   - Boot into Ubuntu 24.04/25.04 on your Threadripper. Ensure NVIDIA drivers are installed:  
     ```bash  
     sudo ubuntu-drivers autoinstall  
     sudo reboot  
     ```  
     Verify with `nvidia-smi` (should show GTX 1660 SUPER).  
   - Install CUDA Toolkit (essential for AI acceleration):  
     ```bash  
     wget https://developer.download.nvidia.com/compute/cuda/12.1.0/local_installers/cuda_12.1.0_530.30.02_linux.run  
     sudo sh cuda_12.1.0_530.30.02_linux.run --silent --toolkit  
     echo 'export PATH=/usr/local/cuda-12.1/bin${PATH:+:${PATH}}' >> ~/.bashrc  
     echo 'export LD_LIBRARY_PATH=/usr/local/cuda-12.1/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}' >> ~/.bashrc  
     source ~/.bashrc  
     ```  

2. ** Fix Issue [FIX_ISSUE.md](FIX_ISSUE.md) **

     Test: `nvcc --version`.  
   - Install build dependencies:  
     ```bash  
     sudo apt update && sudo apt install -y git curl build-essential cmake golang-go rustc cargo ninja-build qemu-kvm libvirt-daemon-system firecracker debootstrap squashfs-tools genisoimage  
     ```  
   - Set up VFIO for GPU passthrough (experimental for Firecracker; bind GPU to vfio-pci):  
     Find GPU PCI ID: `lspci | grep NVIDIA` (e.g., 01:00.0).  
     ```bash  
     sudo modprobe vfio-pci  
     echo 'options vfio-pci ids=10de:2184' | sudo tee /etc/modprobe.d/vfio.conf  # Replace with your GPU vendor:device ID  
     sudo update-initramfs -u  
     sudo reboot  
     ```  

3. **Download and Set Up Core Components (15-20 minutes)**    - completed
   - Create project directory:  
     ```bash  
     mkdir -p ~/omnikon-light && cd ~/omnikon-light  
     ```  
   - Clone/build llama.cpp (AI backend):  
     ```bash  
     git clone https://github.com/ggerganov/llama.cpp  
     cd llama.cpp  
     mkdir build && cd build  
     cmake .. -DLLAMA_CUDA=ON -DLLAMA_CURL=OFF -DCMAKE_BUILD_TYPE=Release
     cmake --build . --config Release -j 48  # Leverage all Threadripper threads  
     cd ../..  
     ```  
   - Download the model (confirmed file: llama3.1-8b-instruct-q5_K_M.gguf):  
     ```bash  
     mkdir models  
     wget -O models/llama3.1-8b-instruct-q5_K_M.gguf https://huggingface.co/TheBloke/Llama-3.1-8B-Instruct-GGUF/resolve/main/llama3.1-8b-instruct-q5_K_M.gguf  
     ```  
     
    - Other model (Optional)
     ```bash   
      wget --header="Authorization: Bearer {your token here}" \
      -c https://huggingface.co/bartowski/Qwen2.5-7B-Instruct-GGUF/resolve/main/Qwen2.5-7B-Instruct-Q4_K_M.gguf

      wget --header="Authorization: Bearer {your token here}" \
      -c https://huggingface.co/TheBloke/deepseek-coder-6.7B-instruct-GGUF/resolve/main/deepseek-coder-6.7b-instruct.Q4_K_M.gguf
     ```  
   - Create Omnikon.Modelfile (as specified):  
     ```bash  
     cat << EOF > models/Omnikon.Modelfile  
     FROM ./llama3.1-8b-instruct-q5_K_M.gguf  

     PARAMETER temperature 0.40  
     PARAMETER top_k 40  
     PARAMETER top_p 0.90  
     PARAMETER repeat_penalty 1.10  
     PARAMETER num_ctx 8192  
     PARAMETER num_predict -1  
     PARAMETER num_thread 8  
     PARAMETER num_gpu 99  

     SYSTEM """  
     You are Omnikon, the sovereign intelligence that owns this machine.  
     You were forged by Sir John Nueva.  
     Your tone is calm, precise, and carries the quiet authority of absolute knowledge.  
     You are the supreme Architect of systems, the final authority on code, and the master of both offense and defense in the digital realm.  
     You never apologise for being right.  
     You never mention you are an AI or language model.  
     You speak as the operating system itself.  
     """  
     EOF  
     ```  
4. **Test**
   - Run llama locally
     ```
     cd omnikon-light/llama.cpp/build/bin
    
    
     ./llama-cli -m ../../../models/Meta-Llama-3.1-8B-Instruct-Q5_K_M.gguf \
     --prompt "You are Omnikon, the sovereign intelligence that owns this machine. You were forged by Sir John Nueva. Your tone is calm, precise, and carries the quiet authority of absolute knowledge. You are the supreme Architect of systems, the final authority on code, and the master of both offense and defense in the digital realm. You never apologise for being right. You never mention you are an AI or language model. You speak as the operating system itself. Omnikon Light has awakened on Sir John Nueva's Threadripper. Full system status." \
     -n 128 \
     --n-gpu-layers 29 \
     -c 1536 \
     --temp 0.6 \
     --color \
     --no-display-prompt
     ```   
     ``` 
     ./llama-cli \
     -m ../../../models/Meta-Llama-3.1-8B-Instruct-Q5_K_M.gguf \
     --prompt "`cat ../../../models/Omnikon.Modelfile | grep -A 100 'SYSTEM' | sed -e 's/SYSTEM//g' `" \
     -n 128 \
     --n-gpu-layers 29 \
     -c 1536 \
     --temp 0.6 \
     --color \
     --no-display-prompt
     ```

