# Smoothing Priors for Blind Image Deblurring

Official MATLAB implementation of the paper:

> Haobo Xu and Fang Li, "Smoothing Priors for Blind Image Deblurring,"
> SIAM Journal on Imaging Sciences, 18(1):216-245, 2025.
> DOI: [10.1137/24M1637696](https://doi.org/10.1137/24M1637696)

## Abstract

Blind image deblurring aims to recover a sharp latent image and estimate the unknown blur kernel from a blurry observation. This work introduces a deblurring algorithm built on image smoothing priors. The key observation is that salient structural edges are especially important for blur-kernel estimation, while fine textures and unnecessary details can disturb the estimation process.

To address this, the method uses smoothing-inspired regularization to preserve meaningful edges and suppress distracting artifacts during intermediate latent-image estimation. The model combines relative total variation with nonconvex intensity and gradient priors, and it is further extended to binary-image and non-uniform deblurring settings.

The optimization problem is solved with a half-quadratic splitting strategy. A template-based interpolation scheme is also used for the $L_p$ minimization subproblem. Experiments on multiple datasets show that the proposed method can estimate blur kernels accurately and reduce deblurring artifacts compared with related methods.

## Repository Contents

The MATLAB source code is provided in:

```text
Smoothing priors blind deblur codes.rar
```

After extracting the archive, the main files are:

```text
Smoothing priors blind deblur codes/
|-- Mytest_smoothing.m              # Demo entry point
|-- blind_deconv_smooth.m           # Multi-scale blind deconvolution
|-- blind_deconv_main_smooth.m      # Single-scale kernel estimation loop
|-- smoothing_lp_new.m              # Latent image estimation with smoothing priors
|-- solve_Lp_.m                     # Lp pixel subproblem
|-- solve_lp_grad.m                 # Lp/Lq gradient subproblem helper
|-- ringing_artifacts_removal.m     # Final non-blind deconvolution step
|-- lp_base_0.01.mat                # Precomputed lookup/interpolation bases
|-- lp_base_0.001.mat               # Precomputed lookup/interpolation bases
|-- dataset/blurred_images/         # Example blurry inputs
|-- cho_code/                       # Helper functions adapted from Cho/related code
`-- whyte_code/                     # Helper functions adapted from Whyte et al.
```

## Requirements

The code was developed for MATLAB.

Recommended environment:

- MATLAB with Image Processing Toolbox
- Parallel Computing Toolbox and a CUDA-capable GPU
- An archive extractor that can open `.rar` files, such as WinRAR, 7-Zip, or `bsdtar`

The released demo uses MATLAB `gpuArray` in the fast implementation of the $L_p$ subproblems. If you want to run the code on CPU only, you may need to remove or adapt the `gpuArray`/`gather` calls in files such as `Mytest_smoothing.m`, `smoothing_lp_new.m`, `solve_Lp_.m`, `solve_lp_grad.m`, and `solve_lp_pixel.m`.

## Quick Start

1. Clone this repository:

```bash
git clone https://github.com/xhb-61/Smoothing-priors-for-blind-deblurring-codes.git
cd Smoothing-priors-for-blind-deblurring-codes
```

2. Extract the MATLAB archive:

```bash
bsdtar -xf "Smoothing priors blind deblur codes.rar"
```

You can also extract it manually with WinRAR or 7-Zip.

3. Open MATLAB and enter the extracted folder:

```matlab
cd('path/to/Smoothing priors blind deblur codes')
```

4. Run the demo:

```matlab
Mytest_smoothing
```

By default, the demo reads a sample image from `dataset/blurred_images`, estimates a blur kernel, computes an intermediate latent image, and displays the final deblurred result.

## Running on Your Own Images

Place your blurry images in:

```text
Smoothing priors blind deblur codes/dataset/blurred_images/
```

Then edit the input section of `Mytest_smoothing.m`. The demo currently uses a filename pattern such as:

```matlab
im01_ker01_blur.png
```

For a custom image, you can directly replace the `filename` assignment with your image name, for example:

```matlab
filename = 'my_blurry_image.png';
```

You may also need to tune the kernel size and regularization parameters:

| Parameter | Location | Meaning |
| --- | --- | --- |
| `opts.kernel_size` | `Mytest_smoothing.m` | Estimated blur-kernel size |
| `opts.xk_iter` | `Mytest_smoothing.m` | Number of kernel/latent-image updates per scale |
| `lambda_pixel` | `Mytest_smoothing.m` | Pixel-intensity regularization weight |
| `lambda_grad` | `Mytest_smoothing.m` | Gradient regularization weight |
| `smo.lam` | `Mytest_smoothing.m` | RTV/image-smoothing strength |
| `smo.p` | `Mytest_smoothing.m` | Pixel $L_p$ exponent |
| `smo.q` | `Mytest_smoothing.m` | Gradient $L_q$ exponent |
| `saturation` | `Mytest_smoothing.m` | Switch for saturated-image deconvolution |
| `weight_ring` | `Mytest_smoothing.m` | Ringing artifact suppression weight |

For larger or stronger blur kernels, increase `opts.kernel_size`, for example from `19` to `37`.

## Outputs

The demo displays:

- the input blurry image,
- the intermediate latent image,
- the estimated blur kernel,
- the final deblurred image.

The script also contains a commented recording block near the end of `Mytest_smoothing.m`. Uncomment the `imwrite` lines if you want to save the estimated kernel, intermediate image, and final result.

## Dataset Notes

The archive includes two sample blurry images for a quick smoke test. Additional benchmark data can be downloaded from the original dataset/project pages, for example:

- Pan et al. text deblurring project: <https://jspan.github.io/projects/text-deblurring/index.html>
- Kohler benchmark dataset: please follow the original dataset release and license terms.

Please respect the licenses and usage requirements of external datasets.

## Troubleshooting

### `gpuArray` errors

The current fast implementation uses GPU arrays. Make sure MATLAB can access a compatible GPU:

```matlab
gpuDevice
```

If no GPU is available, adapt the GPU-related code paths to CPU arrays.

### RAR extraction problems

If one extractor cannot open the archive, try WinRAR, a recent 7-Zip version, or `bsdtar`:

```bash
bsdtar -xf "Smoothing priors blind deblur codes.rar"
```

### `L0Restoration` is undefined

The default demo sets:

```matlab
weight_ring = 0;
```

With this setting, the code returns after the TV-based non-blind deconvolution step and does not call `L0Restoration`. If you enable ringing suppression with `weight_ring > 0`, make sure the corresponding L0 restoration dependency is available on your MATLAB path.

## Citation

If this repository is useful for your research, please cite:

```bibtex
@article{xu2025smoothing,
  title={Smoothing priors for blind image deblurring},
  author={Xu, Haobo and Li, Fang},
  journal={SIAM Journal on Imaging Sciences},
  volume={18},
  number={1},
  pages={216--245},
  year={2025},
  publisher={SIAM},
  doi={10.1137/24M1637696}
}
```

## License

This repository is released under the MIT License. Please also respect the licenses and citation requirements of the bundled third-party helper code and any external datasets you use.
