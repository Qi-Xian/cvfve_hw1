# Color-Tranfer Project

Using Pytorch for implementation of CycleGAN (https://arxiv.org/abs/1703.10593)

## Goal
1.  (Training cycleGAN)
2.  (Inference cycleGAN in personal image)
3.  (Compare with other method), This method called **fast-coler transfer**

## Getting Started（實作步驟）
Please firstly install [Anaconda](https://anaconda.org), if you not understand how to install whole procedures on Ubuntu system, you can take this [link](https://stackoverflow.com/questions/28852841/install-anaconda-on-ubuntu-via-command-line) as reference.


After finishing, you need to create an Anaconda environment using the environment.yml file.

```
conda env create -f environment.yml
```

After you create the environment, activate it.
```
source activate hw1
```

Our current implementation supports GPU Card (Such as GTX-1060 up), you must have one GPU (like GTX-1080 ti) and have CUDA libraries installed on your machine. 

**(Don't use VMs running on Ubuntu Operation, because VMs can not get the real GPU card)**

## Training（開始訓練）
### 1. Download dataset（下載資料集）

your need to create one folder named datasets
```
mkdir datasets
```

and then,
```
cd datasets
```
Implement the instruction
```
bash ./download_dataset.sh <dataset_name>
```
Valid <dataset_name> are: apple2orange, summer2winter_yosemite, horse2zebra, monet2photo, cezanne2photo, ukiyoe2photo, vangogh2photo, maps, cityscapes, facades, iphone2dslr_flower, ae_photos (Here we use apple2roange)

Alternatively you can build your own dataset by setting up the following directory structure:

    .
    ├── datasets                   
    |   ├── <dataset_name>         # i.e. apple2orange
    |   |   ├── trainA             # Contains domain A images (i.e. apple)
    |   |   ├── trainB             # Contains domain B images (i.e. orange) 
    |   |   ├── testA              # Testing
    |   |   └── testB              # Testing
    
### 2. Train（訓練）

Start to train, type the command as follow: 

```
python train.py --dataroot datasets/<dataset_name>/ --cuda
```

This command would start a training session using the images under the *dataroot/train* directory with the hyperparameters that showed best results according to CycleGAN authors. 

Both generators and discriminators weights will be saved ```./output/<dataset_name>/``` the output directory.

**If you don't own one GPU, remove the --cuda option, but you had better get one, becaue it is faster than CPU !**

## Testing（測試）
After training, we can start to test.

The pre-trained file is on [Google drive](https://drive.google.com/open?id=17FREtttCyFpvjRJxd4v3VVlVAu__Y5do). Download the file and save it on  ```./output/<dataset_name>/netG_A2B.pth``` and ```./output/<dataset_name>/netG_B2A.pth```. 

```
python test.py --dataroot datasets/<dataset_name>/ --cuda
```
This command will take the images under the ```dataroot/testA/``` and ```dataroot/testB/``` directory, run them through the generators and save the output under the ```./output/<dataset_name>/``` directories. 

Examples of the generated outputs (default params) apple2orange, summer2winter_yosemite, horse2zebra dataset:

![Alt text](./output/imgs/0035.png)
![Alt text](./output/imgs/0111.png)
![Alt text](./output/imgs/0167.png)

**Next we use our Personal image to test.**

output/apple2orange/A folder, we test 3 pictures：

![Alt text](./output/apple2orange/A/0001.png)
![Alt text](./output/apple2orange/A/0002.png)
![Alt text](./output/apple2orange/A/0003.png)

output/apple2orange/B folder：

![Alt text](./output/apple2orange/B/0001.png)
![Alt text](./output/apple2orange/B/0002.png)
![Alt text](./output/apple2orange/B/0003.png)

After using CycleGAN for train and test, we also use the approach of **fast-coler transfer** to compare.

### Requirements（需求）
- OpenCV
- boost/program_options*

### Running the tests, we have three parts of source, Target, Result:

------------------------> **Source**  ------------------------> **Target** ------------------------> **Result**
如下所述，我們將使用Source, 將Source的照片風格元素transfer至Target，最後第三張產生對應的結果。
![Alt text](./color_transfer/applecombined1.jpeg)
![Alt text](./color_transfer/applecombined2.jpeg)
![Alt text](./color_transfer/applecombined3.jpeg)

![Alt text](./color_transfer/orangecombined1.jpeg)
![Alt text](./color_transfer/orangecombined2.jpeg)
![Alt text](./color_transfer/orangecombined3.jpeg)

### 原理

1、RGB三通道有很強的關聯性，而做顏色的改變同時恰當地改變三通道比較困難。

2、需要尋找三通道互不相關的也就是正交的顏色空間，作者想到了Ruderman等人提出的lαβ顏色空間。三個軸向正交意味著改變任何一個通道都不影響其他通道，從而能夠較好的保持原圖的自然效果。三個通道分別代表：亮度，黃藍通道，紅綠通道。

顏色遷移算法步驟：

RGB->LMS->對數LMS->lαβ->對數LMS->LMS->RGB，在lαβ上進行適當的變換。

RGB->LMS分為兩步：RGB->XYZ->LMS



### References:
[Super fast color transfer between images](https://github.com/jrosebr1/color_transfer)

## Acknowledgments

Code is modified by [PyTorch-CycleGAN](https://github.com/aitorzip/PyTorch-CycleGAN). All credit goes to the authors of [CycleGAN](https://arxiv.org/abs/1703.10593), Zhu, Jun-Yan and Park, Taesung and Isola, Phillip and Efros, Alexei A.




