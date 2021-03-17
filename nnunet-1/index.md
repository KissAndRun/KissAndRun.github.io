# nnUNet 使用不完全指南（下）

## 确定最好的 **UNet** 配置
当模型训练完成之后，使用下面�的链接自动选择合适的 UNet 配置。 
```
nnUNet_find_best_configuration -m 2d 3d_fullres 3d_lowres 3d_cascade_fullres -t XXX --strict
```
注意：五折交叉验证需要全部训练完成。同理 XXX 代表你的任务序号，**--strict** 参数代表即使配置不存在仍继续执行。

## 进行标签图的预测
**nnUNet_find_best_configuration** 会打印出你所需要的命令。当然你也可以手动选择 **UNet** 配置进行标签图的预测。命令如下：
```
nnUNet_predict -i INPUT_FOLDER -o OUTPUT_FOLDER -t TASK_NAME_OR_ID -m CONFIGURATION --save_npz
```
注意： 这里的 **INPUT_FOLDER** 也需要之前的文件结构和文件命名（如果之前已经设置好了，就不必修改了）。**-o** 代表输出目录， **-m** 代表所使用的 **UNet** 配置。
nnUNet 预测所使用的时间较长，需要耐心等待。

## 其他的一下细节
### 两阶段的 nnUNet 训练
首先要完成 **3d_lowres** 的五折交叉验证。也就是分别运行下面的命令：
```
nnUNet_train 3d_lowres nnUNetTrainerV2 TaskXXX_MYTASK 0 --npz
nnUNet_train 3d_lowres nnUNetTrainerV2 TaskXXX_MYTASK 1 --npz
nnUNet_train 3d_lowres nnUNetTrainerV2 TaskXXX_MYTASK 2 --npz
nnUNet_train 3d_lowres nnUNetTrainerV2 TaskXXX_MYTASK 3 --npz
nnUNet_train 3d_lowres nnUNetTrainerV2 TaskXXX_MYTASK 4 --npz
```
然后再进行两阶段的训练。
```
nnUNet_train 3d_cascade_fullres nnUNetTrainerV2CascadeFullRes TaskXXX_MYTASK FOLD --npz
```

### 继续训练
如果训练的一半意外停止了可以在原命令后添加 **-c** 参数继续训练。


