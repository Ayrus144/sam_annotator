# SAM Annotation Tool (SAMAT)

SAMAT is a hackable PyQt5 based GUI labelling tool for semantic segmentation task enhanced by an assistance feature, reminiscent of the functionality offered by the Photoshop Magic Wand tool.

**Note:** Magic Wand is **optional** tool and uses SAM masks that can be generated by python script in `scripts` directory of this repo.

## Showcase

Animation below shows annotation speed in real-time (SAM mask used).
![showcase](assets/showcase.gif)

## Workflow

After environment setup:
- Organize your data following [this](#dataset-folder-structure) structure. Don't forget to copy/create/edit `classes.json`.
- (optional) Generate SAM masks from images via `scripts/preprocess_dataset.py`. Necessary for `SAM assistance` option.
```bash 
python scripts/preprocess_dataset.py
```
- Specify path to your data in `config.toml`.
- Run GUI via `__main__.py` ([prerequisites](#prerequisites) should be satisfied).
```bash 
python __main__.py
```
- Annotate using brush (and/or) SAM assistance (label is saved on sample switch).
- See **Shortcuts** section for brush and panel controls (ex. changing image sample etc.)

## Getting started

### Prerequisites: Environment setup and SAM Model

Annotation tool itself requires only:

- `Python 3.11`
- `PyQt5`
- `numpy`

(optional) In order to generate SAM masks for Magic Wand, you will need to install:

- `PyTorch` to run SAM model inference
- `segment-anything` itself + related libs
- Download `ViT-H` SAM model from https://github.com/facebookresearch/segment-anything and place the subsequent `sam_vit_h_4b8939.pth` file in `assets` folder.

(Hint) Setting up conda environment with `requirements.txt` should cover the prerequisites. But downloading `ViT-H` model should still be done manually.

Example setup (assuming Miniconda/Anaconda installed):

```bash
conda create -n samat python=3.11
conda activate samat

## Environment setup with requirements.txt
pip install -r requirements.txt

## Or, Manual environment setup
## conda install pytorch torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia
## pip install git+https://github.com/facebookresearch/segment-anything.git
## pip install opencv-python pycocotools matplotlib onnxruntime onnx
```

Example setup (Ubuntu):

```bash
git clone https://github.com/Divelix/samat.git
cd samat
sudo apt update
sudo apt install python3.11-venv
pip3 install virtualenv
virtualenv venv -p python3.11
source venv/bin/activate
python -m pip install -e .
python .
```


### Dataset folder structure

Your data **MUST** follow this structure:
```
── my_dataset
   ├── images
   |   ├── 000001.jpg
   |   ├── 000002.jpg
   |   └── ...
   ├── labels (optional)
   |   ├── 000001.jpg
   |   ├── 000002.jpg
   |   └── ...
   ├── sam (optional)
   |   ├── 000001.jpg
   |   ├── 000002.jpg
   |   └── ...
   └── classes.json
```

- `images` contains `.jpg` files you want to label.
- `labels` contains `.jpg` files with labels (will be automatically created if you have no labels yet).
- `sam` contains `.jpg` files with SAM annotations (8-bit grayscale product of SAM script from `scripts/` folder).
- `classes.json` contains classes description that will be used for labeling.


**Hints & Best Practice**: 
- To maintain similarity among multiple sessions, save a common `classes.json` at `example_dataset` folder and copy it to your required dataset.
- While creating `classes.json` file, be sure to create independent class labels, especially its corresponding color in hexadecimal.

Example `classes.json`:

```json
{
    "classes": [
        { "id": 1, "name": "human", "color": "#FF0000" },
        { "id": 2, "name": "car", "color": "#00FF00" },
    ]
}
```

where:

- `id` field must coincide with number keys on keyboard, so start with 1 (not 0). Any number of classes allowed, but only first 9 have their shortcuts.
- `name` field is arbitrary and used only for dispaly in GUI
- `color` field specifies the color this class would be displayed in GUI and encoded in output label `.jpg`

**Note:** 
- Specify path to your `data` inside `config.toml`.
- Image files can have arbitrary names, but should resemble labels and sam names + only `.jpg` format is supported.

## Shortcuts

|                Shortcut               | Description                                          |
| :------------------------------------:| ---------------------------------------------------- |
|           Left Mouse Button           | Draw with brush + fill region (in SAM mode)          |
|           Right Mouse Button          | Pan motion on zoomed-in image                        |
|              Mouse Wheel              | Zoom in/out                                          |
|          `Ctrl` + Mouse Wheel         | Change brush size                                    |
|                `1`-`9`                | Select class (color to draw on label layer)          |
|                  `E`                  | Eraser tool (transparent brush)                      |
|                `Space`                | Reset zoom                                           |
|                  `C`                  | Clear label                                          |
|                  `S`                  | Switch SAM assistance mode on/off                    |
|               `,`/`.`                 | Previous/Next sample                                 |
