---
Class: Computer Vision
Date: 2026-09-24
tags:
  - Image_Acquisition
  - Digital_Image_Formation
---
## Physics and Photometry of Image Formation

A digital image measures the radiant energy emitted or reflected by objects in a 3D environment and focused by optical elements onto a two-dimensional sensor plane.

### The Five Fundamental Physical Factors
The formation of an image on the camera sensor is governed by five interdependent physical parameters:

1. **Incidental Illumination $E(x,y,z,\lambda)$**: The radiant energy hitting point $(x,y,z)$ in 3D space across wavelength spectrum $\lambda$.
2. **Surface Reflectance Function $r(x,y,z,\lambda)$**: The intrinsic spectral reflectance property of the object material at coordinate $(x,y,z)$.
3. **Reflected Light Spectrum $c(x,y,z,\lambda)$**: The pointwise multiplication of incidental illumination and surface reflectance:
   $$c(x,y,z,\lambda) = E(x,y,z,\lambda) \cdot r(x,y,z,\lambda)$$
4. **3D-to-2D Optical Projection**: Geometric transformation mapping 3D world coordinates $(x,y,z)$ to 2D camera sensor coordinates $(x',y')$, producing light distribution $c_p(x',y',\lambda)$ on the focal plane.
5. **Sensor Spectral Sensitivity $V(\lambda)$**: The efficiency response of sensor elements across different light wavelengths $\lambda$.

---

### 2.2 Empirical Photometric Parameters

#### A. Light Source Radiant Intensities $E(\lambda)$ (measured in Candela)
| Light Source | Typical Intensity (Candela) | Physical Characteristics |
| :--- | :--- | :--- |
| **Solar Light** | **9000** | High-intensity direct sunlight |
| **Cloudy Sky** | **1000** | Diffused ambient daylight |
| **Office Internal Light** | **100** | Standard artificial indoor lighting |
| **Moon Light** | **0.01** | Low-light nocturnal illumination |

#### B. Material Surface Reflectance $r(\lambda)$ (Albedo Values)
| Material / Object | Reflectance Coefficient $r$ | Surface Behavior |
| :--- | :--- | :--- |
| **Snow** | **0.93** | Extremely high diffuse reflection |
| **Silver & Metals** | **0.90** | Specular and high reflective property |
| **White Wall** | **0.80** | Near-Lambertian diffuse reflection |
| **Black Velvet** | **0.01** | Maximum light absorption |

#### C. Light-Surface Interaction Mechanics
When incident light rays collide with a material interface, energy is partitioned into five physical phenomena:
- **Absorption**: Conversion of radiant energy into thermal energy within the material.
- **Diffusion (Lambertian Scattering)**: Uniform reflection of light in all directions regardless of viewing angle.
- **Reflex (Specular Reflection)**: Directional ray bounce obeying the law of reflection ($	heta_{	ext{incident}} = 	heta_{	ext{reflected}}$).
- **Transparency**: Unhindered light transmission through medium interfaces.
- **Refraction**: Ray bending due to refractive index changes across medium boundaries (Snell's Law).

---

### 2.3 Geometric Projection and Continuous Image Equation

#### Orthographic Projection Model
Under the orthographic projection approximation, the projected height $\delta$ on the focal plane remains strictly invariant to object distance $l$ along the optical axis:

$$O_1 = O_2, \quad l_1 < l_2 \implies \delta_1 = \delta_2$$

```
   Sensor / Film Plane                  Object 1          Object 2
     +------------+                        •                 •
     |            |                      l1, Δ1            l2, Δ2
     |   δ1, δ2   |<-----------------------|-----------------|----------> Optical Axis
     |            |
     +------------+
```

For perspective projection modeling and multi-view camera geometries, see [[stereo-vision]] and [[pose-estimation]].

#### Continuous Monochromatic Image Equation
Integrating incident light radiance over the visible spectral domain $\lambda$, the continuous light intensity distribution $f_c(x',y')$ on the sensor array is given by:

$$f_c(x', y') = \int_{\lambda_{\min}}^{\lambda_{\max}} c_p(x', y', \lambda) \, V(\lambda) \, d\lambda$$

---

## 🎨 3. Color Sensing and Perception (RGB)

To replicate human trichromatic vision, digital color camera arrays place three distinct filter types over sensor coordinates $(x',y')$, tuned to **Red (R)**, **Green (G)**, and **Blue (B)** spectral response bands.

```
                  Trichromatic Sensor Array
                          [ Red ]
                         /       \
               [ Green ]  <----->  [ Blue ]
```

The captured continuous intensity distribution for each color channel $C \in \{R, G, B\}$ is defined by integrating with channel-specific spectral sensitivity functions $V_R(\lambda)$, $V_G(\lambda)$, and $V_B(\lambda)$:

$$f_R(x', y') = \int c_p(x', y', \lambda) \, V_R(\lambda) \, d\lambda$$

$$f_G(x', y') = \int c_p(x', y', \lambda) \, V_G(\lambda) \, d\lambda$$

$$f_B(x', y') = \int c_p(x', y', \lambda) \, V_B(\lambda) \, d\lambda$$

For detailed analysis of color spaces (HSV, Lab, YCbCr) and color image processing, refer to [[color-spaces-and-perception]].

---

## 🌌 4. Multidimensional Image Domains

Digital images can be categorized by the spatial and temporal dimension of their domain:

| Domain Dimension | Mathematical Notation | Typical Application | Related Topics |
| :--- | :--- | :--- | :--- |
| **2D Spatial Image** | $f(x,y)$ | Photography, Satellite Scans, Documents | [[histograms-and-intensity-transformations]] |
| **3D Volumetric Image** | $f(x,y,z)$ | CT Scans, MRI Volumes, 3D Voxels | [[image-segmentation]] |
| **2D + Time Video** | $f(x,y,t)$ | Video Streams, Dynamic Motion Analysis | [[optical-flow]], [[object-tracking]], [[action-recognition]] |

---

## 🔢 5. Digitalization: Sampling and Quantization

A projected optical image is continuous in both spatial domain $(x',y') \in \mathbb{R}^2$ and intensity range $f_c(x',y') \in \mathbb{R}$. Digital computers require full discretization.

```
                        DIGITALIZATION PROCESS
                                   │
           ┌───────────────────────┴───────────────────────┐
           ▼                                               ▼
 SPATIAL SAMPLING                                INTENSITY QUANTIZATION
 (Domain Discretization)                         (Co-domain Discretization)
 (x', y') ∈ ℝ² ──► (i, j) ∈ ℕ²                   f_c(x', y') ∈ ℝ ──► f_hat(i, j) ∈ ℕ
```

---

### 5.1 Spatial Sampling & The 2D Comb Function

Spatial sampling measures continuous spatial light distributions at discrete lattice coordinates. Mathematically, 2D samplinqg is modeled as multiplying continuous signal $f_c(x',y')$ by a **2D comb function** ($	ext{comb}(x',y')$) consisting of an infinite array of Dirac delta impulses $\delta$ spaced by sample intervals $\Delta_x$ and $\Delta_y$:

$$	ext{comb}(x', y') = \sum_{i=0}^{N-1} \sum_{j=0}^{M-1} \delta(x' - i\Delta_x, y' - j\Delta_y)$$

The resulting spatially discrete image signal is:

$$f(x'_i, y'_j) = f_c(x', y') 	imes 	ext{comb}(x', y') \implies f(i, j)$$

Under-sampling below the Nyquist frequency leads to spatial aliasing. Anti-aliasing filtering techniques are covered in [[spatial-filtering-and-convolution]].

---

### 5.2 Intensity Quantization & Bit Depth

Quantization partitions continuous brightness levels into $Nc = L = 2^b$ discrete digital values, where $b$ represents the bit depth per channel:

$$f(x'_i, y'_j) \longrightarrow \hat{f}(i, j) \in \{0, 1, 2, \dots, L-1\}$$

In standard $8$-bit grayscale images ($b=8$), intensity is represented by $2^8 = 256$ discrete levels ($0 = 	ext{black}$, $255 = 	ext{white}$).

#### Quantization Artefacts (False Contouring)
Reducing bit depth $b$ restricts available grey levels:
- **$b \ge 8$ ($256$ levels)**: Smooth, imperceptible gray transitions.
- **$b = 4$ ($16$ levels) or $b = 2$ ($4$ levels)**: Severe **false contouring** (posterization) artifacts appear as artificial boundaries across smooth gradient regions.

---

### 5.3 The Pixel and Spatial Resolution

> [!IMPORTANT] Definition of a Pixel
> A **Pixel** (Picture Element) represents the single sampled value on a discrete spatial grid. **It is not an infinitely small mathematical point**, but rather an area element—the smallest resolvable spatial unit captured by a physical sensor element.

#### Display vs. Print Resolution Standards
- **Screen Display / Web**: Standard **72 dpi** (dots per inch) matching digital monitors.
- **High-Quality Print**: Up to **1200 dpi** or higher for continuous-tone output.

---

### 5.4 Exhaustive Memory Storage Requirement Table

The total memory footprint $b_{	ext{total}}$ (in bits) required to store an $N 	imes N$ square digital image with bit depth $k$ ($L = 2^k$) is calculated as:

$$b_{	ext{total}} = N 	imes N 	imes k 	ext{ bits}$$

| Grid $N 	imes N$ | $k=1$ ($L=2$) | $k=2$ ($L=4$) | $k=3$ ($L=8$) | $k=4$ ($L=16$) | $k=5$ ($L=32$) | $k=6$ ($L=64$) | $k=7$ ($L=128$) | $k=8$ ($L=256$) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **32 × 32** | 1,024 b | 2,048 b | 3,072 b | 4,096 b | 5,120 b | 6,144 b | 7,168 b | 8,192 b |
| **64 × 64** | 4,096 b | 8,192 b | 12,288 b | 16,384 b | 20,480 b | 24,576 b | 28,672 b | 32,768 b |
| **128 × 128** | 16,384 b | 32,768 b | 49,152 b | 65,536 b | 81,920 b | 98,304 b | 114,688 b | 131,072 b |
| **256 × 256** | 65,536 b | 131,072 b | 196,608 b | 262,144 b | 327,680 b | 393,216 b | 458,752 b | 524,288 b |
| **512 × 512** | 262,144 b | 524,288 b | 786,432 b | 1,048,576 b | 1,310,720 b | 1,572,864 b | 1,835,008 b | **2,097,152 b** (~256 KB) |
| **1024 × 1024**| 1,048,576 b | 2,097,152 b | 3,145,728 b | 4,194,304 b | 5,242,880 b | 6,291,456 b | 7,340,032 b | **8,388,608 b** (~1 MB) |
| **2048 × 2048**| 4,194,304 b | 8,388,608 b | 12,582,912 b | 16,777,216 b | 20,971,520 b | 25,165,824 b | 29,369,128 b | **33,554,432 b** (~4 MB) |
| **4096 × 4096**| 16,777,216 b | 33,554,432 b | 50,331,648 b | 67,108,864 b | 83,886,080 b | 100,663,296 b | 117,440,512 b | **134,217,728 b** (~16 MB) |
| **8192 × 8192**| 67,108,864 b | 134,217,728 b | 201,326,592 b | 268,435,456 b | 335,544,320 b | 402,653,184 b | 469,762,048 b | **536,870,912 b** (~64 MB) |

---

## 📂 6. Digital Image Data Structures

Computers store and manipulate digital images using four primary structural formats:

```
                          DIGITAL IMAGE FORMAT TYPES
                                     │
   ┌───────────────────┬─────────────┴─────────────┬───────────────────┐
   ▼                   ▼                           ▼                   ▼
MONOCHROME          TRUECOLOR (RGB)             BINARY              INDEXED
(M × N Matrix)      (M × N × 3 Tensor)          (M × N 1-bit)       (Index + Palette)
8 bits/pixel        24 bits/pixel               0/1 values          K x 3 Look-up Table
```

### 1. Monochrome / Grayscale Image
- **Structure**: 2D matrix of dimension $M 	imes N$.
- **Storage**: Each element $(i,j)$ holds a single scalar integer $\hat{f}(i,j) \in [0, 255]$.

### 2. Truecolor RGB Image
- **Structure**: 3D tensor of dimension $M 	imes N 	imes 3$.
- **Storage**: Every coordinate contains a 3-element vector $[R, G, B]^T$, totaling 24 bits/pixel ($16.7 	imes 10^6$ unique colors).

### 3. Binary Image
- **Structure**: 2D boolean/bit matrix of dimension $M 	imes N$.
- **Storage**: Values restricted to $0$ (background / black) and $1$ (foreground / white). Used extensively in [[image-segmentation]] and [[blob-detection]].

### 4. Indexed Image
- **Structure**: Matrix $X$ of size $M 	imes N$ containing integers in $\{1, 2, \dots, K\}$, coupled with a Color Map (Palette Matrix) $Map$ of size $K 	imes 3$.
- **Storage**: Color values $Map(k, :) = [R_k, G_k, B_k]$ are normalized floating-point numbers in $[0.0, 1.0]$.
- **Memory Optimization**: Storing an $8$-bit index matrix with a palette of $K=256$ RGB colors reduces memory drastically compared to 24-bit RGB tensors.

---

## 🔍 7. Spatial Resampling and Interpolation Algorithms

Modifying image spatial dimensions involves resampling the image function over a new coordinate grid.

- **Downsampling (Sub-sampling)**: **Irreversible** process resulting in permanent loss of high-frequency visual details.
- **Upsampling (Zooming)**: Requires mapping new grid coordinates $(x_{	ext{new}}, y_{	ext{new}})$ back to original image space coordinates $(x_{	ext{old}}, y_{	ext{old}})$ and interpolating intensity values.

---

### Exhaustive Interpolation Methods

#### 1. Nearest Neighbor Interpolation
Assigns the intensity value of the nearest integer sample point in the original grid:

$$f(x, y) = I(	ext{round}(x), 	ext{round}(y))$$

> [!CAUTION] Implementation Hazard
> Programming languages like C/C++ perform truncated integer division by default (`floor`), which causes systematic spatial pixel shifts. Correct implementations must explicitly use `round(x)`. Nearest neighbor interpolation exhibits severe blockiness (pixelation).

#### 2. Triangle Interpolation (Scattered Data)
Used for interpolating intensity over non-grid, scattered 2D data points. The domain is partitioned via Delaunay triangulation. For a target point $q$ located inside a triangle formed by vertices $V_1, V_2, V_3$:

$$Q = V_1 A_1 + V_2 A_2 + V_3 A_3$$

where $A_1, A_2, A_3$ are sub-triangle areas directly opposite to vertices $V_1, V_2, V_3$, normalized by total triangle area. Vertices closer to point $q$ contribute greater weight.

#### 3. Bilinear Interpolation
Evaluates target intensity using the $4$ nearest neighboring pixels $V_1, V_2, V_3, V_4$ surrounding point $q=(x,y)$ in a unit cell.

```
       V1 (x1,y1) ─────── q1 ─────── V2 (x2,y1)
           │               │            │
           │      A4       │     A3     │
           │            d3 │            │
           ├────── d1 ──── q ─── d2 ────┤
           │            d4 │            │
           │      A2       │     A1     │
           │               │            │
       V3 (x1,y2) ─────── q2 ─────── V4 (x2,y2)
```

1. **Calculate Distance Fractional Offsets**: $d_1, d_2, d_3, d_4$.
2. **Compute Opposite Rectangle Areas**:
   $$A_1 = d_2 \cdot d_4, \quad A_2 = d_1 \cdot d_4, \quad A_3 = d_2 \cdot d_3, \quad A_4 = d_1 \cdot d_3$$
3. **Weighted Sum Combination**:
   $$q(x,y) = V_1 A_1 + V_2 A_2 + V_3 A_3 + V_4 A_4$$

Bilinear interpolation produces smooth gray-level transitions and eliminates blockiness with minimal computational overhead (4 texture lookups).

#### 4. Bicubic Interpolation
Considers a $4 	imes 4$ neighborhood ($16$ nearest neighbors). Fits 2D cubic polynomials $f(x) = a + bx + cx^2 + dx^3$ along grid directions:
- Preserves smooth intensity derivatives across pixel boundaries.
- Suppresses star artifacts and ringing, producing superior sharp edges.

---

### 7.2 Method Comparison Matrix

| Interpolation Method       | Neighbor Support        | Computational Complexity | Visual Quality      | Best Suited For                         |
| :------------------------- | :---------------------- | :----------------------- | :------------------ | :-------------------------------------- |
| **Nearest Neighbor**       | 1 pixel ($1 	imes 1$)   | $O(1)$ - Very Low        | Blocky / Pixelated  | Real-time previews, label masks         |
| **Triangle Interpolation** | 3 vertices              | $O(1)$ per triangle      | Linear smooth       | Non-uniform scattered 2D data           |
| **Bilinear**               | 4 pixels ($2 	imes 2$)  | $O(1)$ - Low (4 mults)   | Continuous & Smooth | General resizing, real-time CV          |
| **Bicubic**                | 16 pixels ($4 	imes 4$) | $O(1)$ - Moderate        | High sharpness      | High-resolution medical & photo editing |

---

## 🕸️ 8. Digital Geometry, Connectivity, and Topology

In a discrete digital grid $\mathbb{N}^2$, spatial topological relations between neighboring pixels $p=(x,y)$ and $q=(s,t)$ must be strictly defined.

### 8.1 Pixel Neighborhood Relations
- **4-Neighbors $N_4(p)$**: Horizontal and vertical adjacent pixels:
  $$N_4(p) = \{(x+1,y), (x-1,y), (x,y+1), (x,y-1)\}$$
- **Diagonal Neighbors $N_D(p)$**: Corner adjacent pixels:
  $$N_D(p) = \{(x+1,y+1), (x+1,y-1), (x-1,y+1), (x-1,y-1)\}$$
- **8-Neighbors $N_8(p)$**: Union of 4-neighbors and diagonal neighbors:
  $$N_8(p) = N_4(p) \cup N_D(p)$$

---

### 8.2 Adjacency & Connectivity

Two pixels $p$ and $q$ belonging to an intensity value set $V$ (e.g., $V=\{1\}$ for binary foreground) are adjacent if they are connected according to one of three adjacency criteria:

1. **4-Adjacency**: $p$ and $q$ are 4-adjacent if $q \in N_4(p)$.
2. **8-Adjacency**: $p$ and $q$ are 8-adjacent if $q \in N_8(p)$.
3. **m-Adjacency (Mixed Adjacency)**: $p$ and $q$ with values from $V$ are m-adjacent if:
   - $q \in N_4(p)$, OR
   - $q \in N_D(p)$ **AND** the intersection $N_4(p) \cap N_4(q)$ contains no pixels with intensity values in $V$:
     $$N_4(p) \cap N_4(q) \cap V = \emptyset$$

> [!TIP] Eliminating Topological Ambiguity
> Standard **8-adjacency** creates redundant diagonal connections that lead to ambiguous multiple paths. **m-adjacency** resolves path ambiguity by removing redundant diagonal links.

---

### 8.3 Digital Paths

A digital **path** from pixel $p=(x,y)$ to pixel $q=(s,t)$ is a sequence of distinct pixels:

$$(x_0, y_0), (x_1, y_1), (x_2, y_2), \dots, (x_n, y_n)$$

where $(x_0,y_0) = (x,y)$, $(x_n,y_n) = (s,t)$, and each pixel $(x_i, y_i)$ is adjacent to $(x_{i-1}, y_{i-1})$ for $1 \le i \le n$. Depending on the adjacency type used, paths are classified as **4-paths**, **8-paths**, or **m-paths**.

```
      8-Path (Topological Ambiguity)               m-Path (Unambiguous)
              p ─── o                                    p ─── o
              │ ╲   │                                    │     │
              o ─── q                                    o     q
     (Redundant diagonal shortcut)             (Unique, well-defined path)
```

---

### 8.4 Metric Distance Functions

A distance function $D(p,q)$ between pixels $p=(x,y)$, $q=(s,t)$, and $z=(u,v)$ is a mathematical metric satisfying three core metric axioms:
1. **Non-negativity & Identity**: $D(p,q) \ge 0$, and $D(p,q) = 0 \iff p = q$.
2. **Symmetry**: $D(p,q) = D(q,p)$.
3. **Triangle Inequality**: $D(p,z) \le D(p,q) + D(q,z)$.

#### 1. Euclidean Distance $D_e(p,q)$
$$D_e(p,q) = \sqrt{(x-s)^2 + (y-t)^2}$$
*(Points with $D_e \le r$ form a continuous circular disk).*

#### 2. City-Block / Manhattan Distance $D_4(p,q)$
$$D_4(p,q) = |x - s| + |y - t|$$
*(Points with $D_4 \le r$ form a diamond/rhombus shape. Pixels with $D_4=1$ are the 4-neighbors of $p$).*

```
   City-Block Distance D4 Matrix Array (r=2)
              2
           2  1  2
        2  1  P  1  2
           2  1  2
              2
```

#### 3. Chessboard Distance $D_8(p,q)$
$$D_8(p,q) = \max(|x - s|, |y - t|)$$
*(Points with $D_8 \le r$ form a square grid. Pixels with $D_8=1$ are the 8-neighbors of $p$).*

```
   Chessboard Distance D8 Matrix Array (r=2)
        2  2  2  2  2
        2  1  1  1  2
        2  1  P  1  2
        2  1  1  1  2
        2  2  2  2  2
```

---

## 🔗 Connected Topics in Computer Vision

- ⬅️ [[introduction-to-computer-vision]] - Course Syllabus, Prerequisites, and System Overview
- ➡️ [[color-spaces-and-perception]] - RGB, HSV, Lab, and Human Color Vision Modeling
- ➡️ [[histograms-and-intensity-transformations]] - Histogram Equalization and Intensity Mappings
- ➡️ [[spatial-filtering-and-convolution]] - Image Smoothing, Edge Sharpening, and Filter Kernels
- ➡️ [[edge-detection-techniques]] - Derivatives, Sobel, Canny, and Edge Operators
- ➡️ [[blob-detection]] - Difference of Gaussians, Laplacian of Gaussian, and Hessian Features
- ➡️ [[fitting-and-hough-transform]] - Line and Circle Fitting, RANSAC Algorithm
- ➡️ [[image-segmentation]] - Graph Cuts, K-Means Clustering, Thresholding
- ➡️ [[optical-flow]] - Lucas-Kanade, Horn-Schunck, Motion Vector Fields
- ➡️ [[object-tracking]] - Kalman Filters, Meanshift, Particle Tracking
- ➡️ [[stereo-vision]] - Epipolar Geometry, Disparity Maps, 3D Depth Reconstruction
- ➡️ [[pose-estimation]] - Camera Calibration Matrices, PnP, Keypoint Localization
- ➡️ [[action-recognition]] - Spatio-Temporal Video Analysis and Action Classification

