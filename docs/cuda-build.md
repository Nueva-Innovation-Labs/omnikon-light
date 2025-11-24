# Final llama.cpp Build Command (Omnikon Light v0.1)

```bash
rm -rf build && mkdir build && cd build
cmake .. -DLLAMA_CUDA=ON -DLLAMA_CURL=OFF -DCMAKE_BUILD_TYPE=Release
cmake --build . --config Release -j48