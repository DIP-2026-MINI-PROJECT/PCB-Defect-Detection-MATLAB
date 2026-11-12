# PCB Defect Inspection by Template Comparison

### Project Description:
#### Summary - 
Finds defects on printed circuit boards by comparing each board with a defect-free template of the same design, using only classical image processing in MATLAB (no machine learning, no training data). On scanned boards from DeepPCB it marks extra copper (shorts, spurs, spurious copper) in red and missing copper (opens, mousebites, pin-holes) in blue: **65 of 66 labelled defects found with 4 false alarms on 11 test boards**. The same idea is then carried to **real phone photos of an ESP32 board**: a one-window MATLAB app takes a photo of a good board and of a faulty one through a phone used as a webcam, and boxes every change.

#### Course concepts used - 
1. **Unit 1 – Digital image fundamentals:** quantization to 2 levels, image subtraction, D8 (chessboard) distance for a 2-pixel edge tolerance, 8-connectivity and connected-component labelling, bilinear interpolation.
2. **Unit 2 – Image transforms:** 2-D DFT phase correlation to find and correct the shift between the template and the test image.
3. **Unit 3 – Enhancement and restoration:** stress test with 7 kinds of damage (Gaussian and salt-and-pepper noise, dim and uneven light, periodic stripes, blur, low resolution), each repaired with a matching filter: average and median filters, contrast stretching, histogram equalization, Gaussian low-pass lighting correction, notch filter, unsharp masking.
4. **Unit 4 – Morphology and colour:** dilation and erosion (shown to equal the D8 tolerance), opening and closing, hole filling to find the board in a photo, HSV hue to separate colours of equal brightness, RGB colour distance for the photo comparison.
   
#### Additional concepts used -
1. Projective transform from 4 corner points (8 × 8 linear solve, written by us) to straighten a photo taken at an angle.
2. Live camera capture in MATLAB through a phone used as a webcam (Iriun Webcam + MATLAB Support Package for USB Webcams), with a simple button-based GUI.
   
#### Dataset - 
- **DeepPCB:** https://github.com/tangsanli5201/DeepPCB. 11 template/test pairs (640 × 640) from the test split with their defect labels, in `data/`. Licence in `data/LICENSE-DeepPCB.txt`.
- **Our own photos:** phone photos of an ESP32 development board on white paper, good and with an object added (paper, LED legs, screw), in `data/photos/`.

#### Novelty - 
1. Works on real phone photos of a real board, live, not only on the dataset's clean scans.
2. The board is found automatically in each photo (thresholding, hole filling, opening), so the phone and the board may move a little between the two photos.
3. A measured stress test: 7 kinds of damage and the restoration filter that fixes each, including cases where the wrong filter makes things worse.
4. Shows that the Unit 1 edge tolerance (D8 distance) and Unit 4 dilation/erosion with a 5 × 5 square give exactly the same result (0 pixels differ).
   
### Contributors:
1. Pratyush Kumar (PES1UG24EC155)
2. Harshit Bothra (PES1UG24EC920)
3. Shreyas N (PES1UG24EC202)

### Steps:
1. Clone Repository
```git clone <repo-url>```

2. Install Dependencies
MATLAB with the **Image Processing Toolbox**. For the live app only: the **MATLAB Support Package for USB Webcams** and a phone webcam app (e.g. Iriun Webcam). No Python packages are needed.

3. Run the Code
Open MATLAB, make this folder the Current Folder, then type:

| Command | What it does |
|---|---|
| `main` | Phase 1 detector on all 11 boards, results table and step-by-step figure |
| `stress_test` | Unit 3: damages the images and repairs each with a filter |
| `morphology_demo` | Unit 4: D8 tolerance vs dilation/erosion |
| `color_test` | Unit 4: grayscale vs HSV hue on colour boards |
| `photo_test` | Real photos: compares `data/photos/good.jpg` with `faulty1.jpg` |
| `pcb_app` | Live app: 1. Good board → 2. Faulty board → 3. Inspect (or **Saved photos** without a camera) |
| `save_outputs` | Saves the 11 board results into `outputs/` |

### Outputs:
* Important intermediate steps

  Board 20085291 result (green = label, red = extra copper, blue = missing copper):

  ![Board result](outputs/20085291_result.png)

  Stress test (damage and repair), morphology and colour tests:

  ![Stress test](outputs/stress_test.png)
  ![Morphology](outputs/morphology_demo.png)
  ![Colour](outputs/color_test.png)

* Final output images 

  All 11 board results are in `outputs/` as `<board>_result.png`.

  | Real ESP32 photos | Result |
  |---|---|
  | Paper on the board | 1 change found |
  | Untouched board | 0 changes |
  | LED legs across the board | 4 boxes on the legs, 1 reflection |
  | Screw on the board | 3 boxes on the screw and its shadow, 1 reflection |
  | Screw, second position | 2 boxes, both on the screw |

  ![Screw found](outputs/photo_screw_found_2.png)
  ![Paper found](outputs/photo_paper_found.png)

### References:
1. DeepPCB dataset, https://github.com/tangsanli5201/DeepPCB
2. MathWorks, Image Processing Toolbox documentation, and MATLAB Support Package for USB Webcams.
   
### Limitations and Future Work:
- Needs a defect-free template of the same design; the board must lie on plain light paper for its corners to be found.
- Says where a defect is and whether copper is extra or missing, but not its exact type.
- On photos, reflections on shiny metal parts and very thin bare wire can give partial boxes or a false alarm; fixed, even lighting helps.
- The "found" rule is box overlap, so the numbers cannot be compared directly with other published results.
- Future work: name each defect's type from its shape, merge nearby boxes of one object, and test more boards, cameras and lighting.
