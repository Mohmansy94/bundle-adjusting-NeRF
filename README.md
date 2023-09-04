
### Trained model results on different activation functions
https://drive.google.com/drive/folders/1dW30MlMMkJhKQNFPMX6dEYAXvQyEyGEc?usp=sharing

### Running the code

- #### 1. Planar image alignment experiment using different activation functions

  Please follow the following steps:

    1. Open Google colab.
    2. Run this notebook, `BARF_using_activation_function.ipynb`, on Google Colab and follow the code instructions for training and visualization.

     
- #### 2. Planar image alignment experiment
  If you want to try the planar image alignment experiment, run:
  ```bash
  python3 train.py --group=<GROUP> --model=planar --yaml=planar --name=<NAME> --seed=3 --barf_c2f=[0,0.4]
  ```
  This will fit a neural image representation to a single image (default to `data/cat.jpg`), which takes a couple of minutes to optimize on a modern GPU.
  The seed number is set to reproduce the pre-generated warp perturbations in the paper.
  For the baseline methods, modify the arguments similarly as in the NeRF case above:
  - Full positional encoding: omit the `--barf_c2f` argument.
  - No positional encoding: add `--arch.posenc!`.

  A video `vis.mp4` will also be created to visualize the optimization process.

- #### Visualizing the results
  We have included code to visualize the training over TensorBoard and Visdom.
  The TensorBoard events include the following:
  - **SCALARS**: the rendering losses and PSNR over the course of optimization. For BARF, the rotational/translational errors with respect to the given poses are also computed.
  - **IMAGES**: visualization of the RGB images and the RGB/depth rendering.
  
  We also provide visualization of 3D camera poses in Visdom.
  Run `visdom -port 9000` to start the Visdom server.  
  The Visdom host server is default to `localhost`; this can be overridden with `--visdom.server` (see `options/base.yaml` for details).
  If you want to disable Visdom visualization, add `--visdom!`.

  The `extract_mesh.py` script provides a simple way to extract the underlying 3D geometry using marching cubes. Run as follows:
  ```bash
  python3 extract_mesh.py --group=<GROUP> --model=barf --yaml=barf_blender --name=<NAME> --data.scene=<SCENE> --data.val_sub= --resume
  ```
  This works for both BARF and the original NeRF (by modifying the command line accordingly). This is currently supported only for the Blender dataset.

--------------------------------------
### Codebase structure

The main engine and network architecture in `model/barf.py` inherit those from `model/nerf.py`.
This codebase is structured so that it is easy to understand the actual parts BARF is extending from NeRF.
It is also simple to build your exciting applications upon either BARF or NeRF -- just inherit them again!
This is the same for dataset files (e.g. `data/blender.py`).

To understand the config and command lines, take the below command as an example:
```bash
python3 train.py --group=<GROUP> --model=barf --yaml=barf_blender --name=<NAME> --data.scene=<SCENE> --barf_c2f=[0.1,0.5]
```
This will run `model/barf.py` as the main engine with `options/barf_blender.yaml` as the main config file.
Note that `barf` hierarchically inherits `nerf` (which inherits `base`), making the codebase customizable.  
The complete configuration will be printed upon execution.
To override specific options, add `--<key>=value` or `--<key1>.<key2>=value` (and so on) to the command line. The configuration will be loaded as the variable `opt` throughout the codebase.  
  
  
--------------------------------------

If you find our code useful for your research, please cite
```
@inproceedings{lin2021barf,
  title={BARF: Bundle-Adjusting Neural Radiance Fields},
  author={Lin, Chen-Hsuan and Ma, Wei-Chiu and Torralba, Antonio and Lucey, Simon},
  booktitle={IEEE International Conference on Computer Vision ({ICCV})},
  year={2021}
}
```

Please contact me (chlin@cmu.edu) if you have any questions!
