# 📘 Conway's Game of Life - Complete Project Guide

This guide explains all buttons on the dashboard, what you need to measure for your assignment, what each file does, and how to run on CPU and GPU separately.

---

## 1. 🎛️ Dashboard Buttons Explained

### **Section 1: Compute Backend**
* **`MODE: CPU`**:
  * Runs the simulation on your **CPU (Intel Core i5/i7)** using standard 2D arrays sequentially cell-by-cell.
* **`MODE: CUDA`**:
  * Runs the simulation on your **GPU (NVIDIA GeForce MX350)** with thousands of threads computing cells in parallel.
* **`GLOBAL MEMORY`**:
  * Uses the standard CUDA kernel where every thread reads neighbor cells directly from GPU Global Memory (VRAM).
* **`SHARED MEMORY`**:
  * Uses the advanced CUDA kernel where a $16 \times 16$ thread block first loads a tile + halo boundaries into fast on-chip `__shared__` memory before computing neighbors.

---

### **Section 2: Grid Resolution**
* **`128`, `256`, `512`, `1024`, `2048`**:
  * Selects the size of the grid ($N \times N$).
  * For example, `1024` creates a board of $1024 \times 1024 = 1,048,576$ cells (over 1 million cells).

---

### **Section 3: Simulation Controls**
* **`PLAY / PAUSE`**: Starts or pauses continuous evolution of the cells. (Hotkey: `Spacebar`).
* **`STEP (1 GEN)`**: Advances the simulation by exactly 1 generation step (useful when paused).
* **`RANDOMIZE`**: Fills the grid with randomly scattered living cells (approx. 28% density).
* **`CLEAR BOARD`**: Erases all cells to start fresh.
* **`GOSPER GUN`**: Automatically spawns the famous **Gosper Glider Gun** pattern that continuously generates moving gliders.
* **Interactive Grid (Left Canvas)**: Click and drag your mouse directly on the grid to draw living cells.

---

### **Section 4: HPC Benchmarking & Live Metrics**
* **`RUN 100-ITER BENCHMARK`**:
  * Automatically runs **100 generations on CPU** and **100 generations on CUDA** on the current board.
  * Calculates and displays the exact time taken and the **Speedup multiplier** ($T_{\text{CPU}} / T_{\text{CUDA}}$).

#### **Live Metrics Panel (Bottom Right):**
* **Status**: `RUNNING` or `PAUSED`.
* **Active Backend**: Shows whether CPU, CUDA Global, or CUDA Shared is executing.
* **Grid Resolution**: Shows $N \times N$ dimensions and total cell count.
* **Generation Count**: Total steps computed so far.
* **Step Latency**: Exact time taken in milliseconds (ms) for the single latest generation step.
* **Speedup**: Displays how many times faster the GPU is compared to the CPU (e.g. `110.3X FASTER ON GPU`).

---

## 2. 📈 What You Need to Measure (For Report & Video)

For your assignment report and presentation video, measure and compare the following:

1. **Execution Time for 100 Generations (in milliseconds):**
   * How long CPU takes vs. how long CUDA takes for 100 iterations.
2. **Speedup Factor ($S$):**
   $$\text{Speedup} = \frac{\text{Time}_{\text{CPU}}}{\text{Time}_{\text{CUDA}}}$$
   *(e.g., $4682.91\text{ ms} / 42.44\text{ ms} = \mathbf{110.3\times\text{ Speedup}}$)*
3. **Scaling Behavior with Grid Sizes:**
   * Test across $128 \times 128$, $256 \times 256$, $512 \times 512$, $1024 \times 1024$, $2048 \times 2048$, and $4096 \times 4096$.
   * **Key observation:** For small grids ($128\times128$), GPU speedup is smaller due to kernel launch overhead; for large grids ($2048\times2048$ or $4096\times4096$), GPU delivers massive $\approx 100\times$ speedups.
4. **Kernel Comparison (Global Memory vs. Shared Memory):**
   * Compare throughput between simple global memory vs tiled shared memory.

---

## 3. 📂 Project Files Explained

| File | Purpose |
| :--- | :--- |
| [`src/cpu_life.h`](file:///run/media/tharindu-thilakarathna/New%20Volume/Campus%20work/Semester%2005/HPC%20-%20Dr%20Budditha/Assignment/Conway-s-Game-of-Life/src/cpu_life.h) / [`.cpp`](file:///run/media/tharindu-thilakarathna/New%20Volume/Campus%20work/Semester%2005/HPC%20-%20Dr%20Budditha/Assignment/Conway-s-Game-of-Life/src/cpu_life.cpp) | **CPU Engine:** Sequential 2D array implementation, toroidal wrapping, and CPU timing. |
| [`src/cuda_life.h`](file:///run/media/tharindu-thilakarathna/New%20Volume/Campus%20work/Semester%2005/HPC%20-%20Dr%20Budditha/Assignment/Conway-s-Game-of-Life/src/cuda_life.h) / [`.cu`](file:///run/media/tharindu-thilakarathna/New%20Volume/Campus%20work/Semester%2005/HPC%20-%20Dr%20Budditha/Assignment/Conway-s-Game-of-Life/src/cuda_life.cu) | **CUDA GPU Engine:** 2D thread block kernels ($16\times16$), coalesced memory access, shared memory tiling, and GPU VRAM ping-pong buffers. |
| [`src/benchmark.cu`](file:///run/media/tharindu-thilakarathna/New%20Volume/Campus%20work/Semester%2005/HPC%20-%20Dr%20Budditha/Assignment/Conway-s-Game-of-Life/src/benchmark.cu) | **Automated CLI Benchmark:** Tests all grid sizes for 100 iterations and outputs a formatted table + CSV. |
| [`src/gui_main.cu`](file:///run/media/tharindu-thilakarathna/New%20Volume/Campus%20work/Semester%2005/HPC%20-%20Dr%20Budditha/Assignment/Conway-s-Game-of-Life/src/gui_main.cu) | **GUI & Dashboard:** OpenGL/X11 interface combining the live simulation grid with the dashboard HUD. |
| [`Makefile`](file:///run/media/tharindu-thilakarathna/New%20Volume/Campus%20work/Semester%2005/HPC%20-%20Dr%20Budditha/Assignment/Conway-s-Game-of-Life/Makefile) | **Build Script:** Compiles the C++ and CUDA code using `g++` and `nvcc` with optimization `-O3`. |
| [`benchmark_results.csv`](file:///run/media/tharindu-thilakarathna/New%20Volume/Campus%20work/Semester%2005/HPC%20-%20Dr%20Budditha/Assignment/Conway-s-Game-of-Life/benchmark_results.csv) | **Data Output:** Saved CSV containing the benchmark numbers for your report graphs. |

---

## 4. 🚀 How to Run on CPU and GPU Separately

### **Option A: Interactively via GUI**
1. Launch the application:
   ```bash
   ./conway_gui
   ```
2. **To run on CPU:** Click **`MODE: CPU`**.
3. **To run on GPU:** Click **`MODE: CUDA`**.
4. **To test 100 iterations:** Click **`RUN 100-ITER BENCHMARK`** and watch the metrics update live on screen.

---

### **Option B: Automated CLI Benchmark (For Report Data)**
To run a clean 100-iteration benchmark comparing CPU and GPU across all grid sizes:

```bash
./conway_benchmark 100
```

This will run all tests and display the speedup table directly in your terminal, and save the data to `benchmark_results.csv`.
