# nnUNet 使用不完全指南（上）

# nnUNet 使用教程
**nnUNet** 是一个专门用于医学图像的深度学习程序。使用 **nnUNet** 可以很方便的进行网络的训练和预测。他的集成度很高，能自动的进行合适的预处理，自动的选择合适的网络。 **nnUNet** 的官方 **github** 地址是[https://github.com/MIC-DKFZ/nnUNet](https://github.com/MIC-DKFZ/nnUNet)。在 **github** 里面有英文版的详细教程。网上也有一个不错的教程，地址是[https://blog.csdn.net/weixin_42061636/article/details/107623757](https://blog.csdn.net/weixin_42061636/article/details/107623757)。这两篇再加本教程可以交叉看，如有出入，**github** 上的肯定是权威。本文在[kissandrun.github.io](https://kissandrun.github.io)持续更新。

## 安装
首先是安装部分。默认读者使用**Linux**环境，nnUNet并没有提供其他平台的支持。**nnUNet**是基于**Python**和**PyTorch**。所以首先得安装这两个，这里推荐使用**Anaconda**安装。具体步骤如下：
1. 查看一下系统是否已经安装完**Anaconda**环境。在终端中直接输入**conda**，然后回车。如果出现下面字样以及一堆使用方法，则环境已存在**conda**无需安装**Anaconda**。直接跳至第二步。如果提示命令不存在，则参考[https://blog.csdn.net/lwgkzl/article/details/89329383](https://blog.csdn.net/lwgkzl/article/details/89329383)。（记得安装最新的版本！）

   ![image-20210315214305283](https://i.loli.net/2021/03/15/jzh8N3LCsiltDrb.png)



2. 装完conda，然后新建一个环境，专门用于**nnUNet**。

   ```
   conda create --name nnunet python=3.8
   ```

   然后激活这个环境。（之后如果新开终端，每次都要激活环境）

   ```
   conda activate nnunet
   ```

   详细的conda手册参考：[https://docs.conda.io/projects/conda/en/4.6.1/user-guide/tasks/manage-environments.html#creating-an-environment-with-commands](https://docs.conda.io/projects/conda/en/4.6.1/user-guide/tasks/manage-environments.html#creating-an-environment-with-commands)

   

3. 安装**PyTorch**。终端输入以下命令

   ```
   conda install pytorch torchvision torchaudio cudatoolkit=11.1 -c pytorch -c conda-forge
   ```

   check一下是否成功。

   ![image-20210315215900772](https://i.loli.net/2021/03/15/OhWJzM1laq5PciY.png)

   如果能正确输出，则**pytorch**安装正确，且能使用**GPU**加速。

   

4. 在这个环境中安装**nnUNet**。终端中输入以下命令即可。（也可以使用[csdn上的教程](https://blog.csdn.net/weixin_42061636/article/details/107623757)中的那种安装方式)

   ```
   pip install nnunet
   ```

   check一下是否成功

   ![image-20210315220043816](https://i.loli.net/2021/03/15/UBfhoG6KZ4Tbdjr.png)

   如果能**import nnunet**正常输出，则nnUNet安装正确。而且输入**which nnUNet_train** 会有输出
   
   ![image-20210315231329739](https://i.loli.net/2021/03/15/iDUe3k6HYlfZtzM.png)

##  用nnUNet训练自己的数据

1. 设置环境变量。

   在合适的地方新建如下三个文件夹，分别存放预处理后的图像，原始的图像和训练完成的模型。

   ![image-20210315223930060](https://i.loli.net/2021/03/15/Yu4D7CTZINsg1wy.png)

   修改Home目录下的.bashrc文件，在最后加入下面内容(!**注意这三个目录应该是对应自己的**)

   ```
   export nnUNet_raw_data_base="/media/fabian/nnUNet_raw"                   
   export nnUNet_preprocessed="/media/fabian/nnUNet_preprocessed"
   export RESULTS_FOLDER="/media/fabian/nnUNet_trained_models"
   ```
   保存之后，在终端中输入以下命令
   ```
   source ~/.bashrc
   ```

2. 整理数据

   **nnUNet** 对数据的命名和结构都有严格的要求，需要调整自己的数据才能给**nnUNet**使用。在上面新建的**nnUNet_raw**下面再新建**nnUNet_raw_data**文件夹。然后在**nnUNet_raw_data**下新建文件 **TaskXXX_任务名**。 **XXX**是任务序号(大于100的数，前100被占用了)，任务名取一个好记的英文名。

   最后的文件结构如下

   ```
   nnUNet_raw/nnUNet_raw_data/
   ├── Task001_BrainTumour
   ├── Task002_Heart
   ├── Task003_Liver
   ├── Task004_Hippocampus
   ├── Task005_Prostate
   ├── ...
   ```

   然后在任务文件夹里构建如下结构

   ```
   Task001_BrainTumour/
   ├── dataset.json
   ├── imagesTr
   ├── (imagesTs)
   └── labelsTr
   ```

   **imagesTr** 和 **labelsTr** 分别存放训练的原图和标签。**imagesTs**存放测试的原图。**dataset.json** 下面会说。

3. 图像命名

   **imagesTr** 和 **labelsTr** 中图像的命名也有要求。其中**imagesTr**需要改成类似下图这样，而且必须是 **.nii.gz** 格式。建议写个 **python** 脚本批量重命名并保存成 **.nii.gz** 格式。

   ![image-20210315230106817](https://i.loli.net/2021/03/15/1h4BbdVPoCUyt5r.png)

   **labelsTr**需要改成如下样式，注意原图和标签图序号一一对应，只是原图后面带**_0000**。

   ![image-20210315230322999](https://i.loli.net/2021/03/15/RbCvSf6uEWXtK5M.png)

   **imagesTs**改成如下，无需从0开始标号，可以接着训练集继续标下去，比较方便。

   ![image-20210315232305262](https://i.loli.net/2021/03/15/jL4xWvB2rP1taEo.png)

   

4. **dataset.json**编写

   上文说到这个任务文件夹应该包含一个**dataset.json** 文件。下面是一个例子

   ```json
   {
       "description": "Task128_LungLobe",
       "labels": {
           "0": "0",
           "1": "1",
           "2": "2",
           "3": "3",
           "4": "4",
           "5": "5"
       },
       "licence": "see challenge website",
       "modality": {
           "0": "CT"
       },
       "name": "Task128_LungLobe",
       "numTest": 6,
       "numTraining": 80,
       "reference": "see challenge website",
       "release": "0.0",
       "tensorImageSize": "4D",
       "test": [
           "./imagesTs/LungLobe_080.nii.gz",
           "./imagesTs/LungLobe_081.nii.gz",
           "./imagesTs/LungLobe_082.nii.gz",
           "./imagesTs/LungLobe_083.nii.gz",
           "./imagesTs/LungLobe_084.nii.gz",
           "./imagesTs/LungLobe_085.nii.gz"
       ],
       "training": [
           {
               "image": "./imagesTr/LungLobe_000.nii.gz",
               "label": "./labelsTr/LungLobe_000.nii.gz"
           },
           {
               "image": "./imagesTr/LungLobe_001.nii.gz",
               "label": "./labelsTr/LungLobe_001.nii.gz"
           },
           {
               "image": "./imagesTr/LungLobe_002.nii.gz",
               "label": "./labelsTr/LungLobe_002.nii.gz"
           },
           {
               "image": "./imagesTr/LungLobe_003.nii.gz",
               "label": "./labelsTr/LungLobe_003.nii.gz"
           },
           {
               "image": "./imagesTr/LungLobe_004.nii.gz",
               "label": "./labelsTr/LungLobe_004.nii.gz"
           },
           {
               "image": "./imagesTr/LungLobe_005.nii.gz",
               "label": "./labelsTr/LungLobe_005.nii.gz"
           },
           {
               "image": "./imagesTr/LungLobe_006.nii.gz",
               "label": "./labelsTr/LungLobe_006.nii.gz"
           },
           {
               "image": "./imagesTr/LungLobe_007.nii.gz",
               "label": "./labelsTr/LungLobe_007.nii.gz"
           },
           {
               "image": "./imagesTr/LungLobe_008.nii.gz",
               "label": "./labelsTr/LungLobe_008.nii.gz"
           },
           {
               "image": "./imagesTr/LungLobe_009.nii.gz",
               "label": "./labelsTr/LungLobe_009.nii.gz"
           },
           {
               "image": "./imagesTr/LungLobe_010.nii.gz",
               "label": "./labelsTr/LungLobe_010.nii.gz"
           }
       ]
   }
   ```

   按自己的任务的属性，修改上面的**labels**，**numTest**等等信息。注意：这里**image**的文件名里没有 **_0000** 。但是修改这个json文件夹的工作量是比较大的，也可以写个 **python** 脚本自动生成。下面是例子

   ```python
   from collections import OrderedDict
   from batchgenerators.utilities.file_and_folder_operations import save_json
   
   
   def main():
       foldername = "Task128_LungLobe"
       numTraining = 80
       numTest = 6
       numClass = 6
       json_dict = OrderedDict()
       json_dict['name'] = foldername
       json_dict['description'] = foldername
       json_dict['tensorImageSize'] = "4D"
       json_dict['reference'] = "see challenge website"
       json_dict['licence'] = "see challenge website"
       json_dict['release'] = "0.0"
       json_dict['modality'] = {
           "0": "CT",
       }
       json_dict['labels'] = {i: str(i) for i in range(numClass)}
   
       json_dict['numTraining'] = numTraining
       json_dict['numTest'] = numTest
       json_dict['training'] = [{'image': "./imagesTr/LungLobe_{:0>3d}.nii.gz".format(i),
                                 "label": "./labelsTr/LungLobe_{:0>3d}.nii.gz".format(i)}
                                for i in range(numTraining)]
   
       json_dict['test'] = ["./imagesTs/LungLobe_{:0>3d}.nii.gz".format(i)
                            for i in range(numTraining, numTraining+numTest)]
   
       save_json(json_dict, "./dataset.json")
   
   
   if __name__ == "__main__":
       main()
   
   ```

   需要发挥一点主观能动性，修改一下使之适应自己的任务。

5. 整理完数据，运行下面命令

   ```
   nnUNet_plan_and_preprocess -t XXX --verify_dataset_integrity
   ```

   XXX 代替为你的任务序号。该命令进行预处理。

6. 开始训练。

   单卡训练：执行

   ```
   nnUNet_train 3d_fullres nnUNetTrainerV2 XXX 0
   ```

   意思是对序号为XXX的任务进行第0折交叉验证，训练模式是3d的全像素。一共有五折交叉验证，所以最后一个数为0-4。


   注意：默认在第一块 **gpu**（索引为0）上进行训练，如果想指定某个gpu，请先执行：

   ```
   export CUDA_VISIBLE_DEVICES=X
   ```

    X为你指定的gpu索引。再执行上面的命令。

   

   多卡训练：比如我现在要在0和1两张卡上执行训练：
   先执行 

   ```
   export CUDA_VISIBLE_DEVICES=0,1
   ```

   再执行

   ```
   nnUNet_train_DP 3d_fullres nnUNetTrainerV2_DP XXX 0 -gpus 2
   ```

   实验证明多卡训练速度和单卡训练速度的差别不大，只是为了减少每张卡的使用显存来弥补单卡显存不足的情况。

   参考：[https://blog.csdn.net/weixin_42061636/article/details/107623757](https://blog.csdn.net/weixin_42061636/article/details/107623757)

