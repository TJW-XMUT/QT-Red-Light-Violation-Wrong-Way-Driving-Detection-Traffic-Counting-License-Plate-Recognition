# QT-Red-Light-Violation-Wrong-Way-Driving-Detection-Traffic-Counting-License-Plate-Recognition


# 引言
**本项目使用监控探头对当前路口进行分析，实现了一个多功能智能交通系统。可实现车辆闯红灯检测、行人闯红灯检测、车辆逆行检测、超速检测、交通拥堵预测、车流量统计、车牌检测识别、斑马线检测、车辆压线判断等多种功能。可用于毕设、课设、工业实际项目，可提供YOLOv5/v7/v8/v9/v10/v11多种版本代码、测试视频和详细说明文档。可以部署到树莓派、香橙派、Jetson Nano、瑞芯微RK3588等开发板上。**

 [**效果展示视频：https://www.bilibili.com/video/BV1evVfz6Ehq/?share_source=copy_web&vd_source=138d2e7f294c3405b6ea31a67534ae1a**](https://www.bilibili.com/video/BV1evVfz6Ehq/?share_source=copy_web&vd_source=138d2e7f294c3405b6ea31a67534ae1a) (点击观看完整视频)
 
  [**操作演示视频：https://www.bilibili.com/video/BV1YqVfzuECB/?share_source=copy_web&vd_source=138d2e7f294c3405b6ea31a67534ae1a**](https://www.bilibili.com/video/BV1YqVfzuECB/?share_source=copy_web&vd_source=138d2e7f294c3405b6ea31a67534ae1a) (点击观看完整视频)

**软件功能包括：**

（1）视频流处理。

（2）车辆、行人、摩托车/电动车、红绿灯检测。采用基于coco数据集训练的YOLOv5/v7/v8/v9/v10/v11进行检测。

（3）红绿灯识别(判断当前亮灯种类)。采用自行设计的分类网络对输入的红绿灯图像进行分类。

（4）车辆行人跟踪。

（5）车辆测速、超速识别。统计分析车流量，实现交通拥堵预测。

（6）数据可视化。将视频中的速度和车流量等数据进行可视化。

（7）车辆逆行检测、行人闯红灯检测、车辆未按车道线导向行驶、车辆压线判断。

（8）违规行为抓拍，保存。

（9）车辆行驶方向检测、轨迹记录。

（10）车牌检测、识别。

（11）采用自行设计的分类网络对汽车型号进行判断。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/07e9c1f1d2154476ab7f099b56b216ee.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/dc1dd77f21f640ffbbb86455a28b4946.gif)


---
# 一、总体算法思路
本系统采用多模块设计，将任务分解为检测->识别->跟踪->数据分析、可视化四个阶段。各个子模块之间相互独立，使得系统的鲁棒性大大提高，降低了系统开发的风险。

（1）从视频中读取一帧图片，通过Opencv处理图像，并用滑动窗口检测人行道的位置，检测之后，在图片上标注。

（2）将图片送入YOLO网络中，当检测到traffic light 类时，将图像区域送入CNN三分类网络，判断当前红绿灯类型。当检测到car类时，将car类对应的区域添加到目标跟踪列表，当检测到行人时，通过综合考虑CNN网络输出值和第一步得到的人行道检测值判断行人是否闯红灯。

（3）通过目标跟踪获取前8帧和当前帧的位移，计算得到车辆移动速度和车辆移动方向，同时判断车辆是否超速。

（4）同时将目标跟踪框内图片送入车型识别网络，和车牌检测网络。将得到的结果输出并和车辆ID绑定。

（5）通过红灯时车辆移动方向判断车辆闯红灯行为。

（6）抓拍、保存违规行为。

（7）将得到的结果存入CSV表格中。

（8）将CSV表格中的数据进行可视化并展现出来。

---
# 二、代码和文件
## 1. 项目文件说明
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a5b6cdfef8894e12a30c54c0655612c2.png)

## 2. 项目的依赖包
* CUDA 10.1以上
* Python 3.7以上
* python 相关依赖包（详见根目录下requirements.txt）
## 3. 环境安装配置

（1）安装项目目录下的LAVFilters-0.73.1.exe解码器。

（2）切换到项目根目录运行：`pip install –r requirements.txt`。建议使用豆瓣源进行安装。

（3） 安装相关问题：
* 安装torch如果提示找不到版本：
运行`pip install torch==1.4.0 -f https://download.pytorch.org/whl/torch_stable.html`，之后再`pip install torchvision`。
* 如果提示找不到QWebEngineWidgets，则需要另外运行`pip install PyQtWebEngine。`
* 如果提示找不到mkl-fft，可将requirements.txt 目录下的：
```python
mkl-fft==1.0.14
mkl-random==1.1.0
mkl-service==2.3.0
```
删除，再从第一步开始。
* 如果提示没有pyecharts，则运行`pip install pyecharts==0.5.11`。

---
# 三、项目运行过程
## 1. 运行项目
输入：`python main.py`或在pycharm中直接右键点击运行，弹出项目主界面。点击项目左下角文件夹图标，选择待检测的测试视频。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ede216df5f3546769480d0f3a14da729.png)
会弹出预标注窗口，为了提高识别的准确率，可以对一些场景进行预先标注（不是必须），程序仍会自动检测。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ce12151d52844cdebdec616fe0adc30e.png)
任意选择几项（可以不选），点击确定。注意检测压线必须勾选车道线选项，标注方式为从上到下，两点确定一条直线。如果当前场景红绿灯数量过多，推荐勾选红绿灯选项，标注方式为标注矩形左上角和右下角的两个点，人行道标注同理。为了检测车辆是否按照导向行驶，务必勾选前行车道线等选项，注意该选项只是用来标注区域，并不用来判断压线，压线请勾选最后车道线的选项。标注方式为标注两条车道线，先标注左车道线（从上到下），再标注右车道线（从上到下）。选定矩形区域的左上角和右下角点（可唯一确定一个矩形），按y确定选择。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c85bfa25667147b2ad490d3eed85f198.png)
点击左上角第一个选项卡，会弹出live界面，该界面实时展示检测界面，右上为违规检测界面，右下为车辆信息界面。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bd82427a80bc4959a3843892b8e5ba2d.png)
检测完成后，会在项目根目录下的output目录生成video-02文件夹（该文件夹名视你处理的），如下文件。


![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f1fec0538af048ec8c05c3475d4db41a.png)
（1）chart文件夹，保存检测结果的数据可视化文件
（2）csv文件夹，保存检测的结果
（3）illegal文件夹，保存违法行为的抓拍
（4）video-02_processed.mp4，保存处理后的视频文件
## 2. 扇形图、折线图等统计结果获取
处理结束，或者点击进度条窗口的停止处理按钮。再次点击左下角的文件夹图标。选择处理后的视频文件。点击左上角第二个按钮：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ee0e16b6be3e42a281c847d09135c72f.png)

点击左上角第三个按钮，得到数据可视化结果和相关数据：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8dad0b9b654748a3a7c9c7e23ed1df49.jpeg)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c2728fda17d74886890eb04c59202032.png)


点击侧边栏第四个按钮，可获得违规人员和车辆抓拍。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/af9fa521d7b64153b0d95c74f1bc2088.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6c52efd72b054c2fa8433c32879dcca7.png)

---
# 四、核心算法介绍
## 1. 车辆闯红灯识别、行人闯红灯识别
一、人行横道/斑马线检测

（1）通过滑动窗口选取感兴趣的区域

（2）总结斑马线的四个特征： 梯度一致性 等间隔 多根线 斑马线比车道线宽 梯度一致性和等间隔是两个强分类特征，其中梯度一致性特征易召回，等间隔特征强精度。目前算法未使用等间隔特征，主要利用梯度一致，另外两个特征辅助。

（3）转逆透视图, 中值滤波，开运算，闭运算预处理后Canny边缘检测，对边缘检测图用sobel求横纵梯度，得到梯度的模值和方向（方向归为0到90度, 即不区分正反），先过滤梯度太小的点。在滑窗内统计0:70度间隔5度的点直方图，取峰值方向的点数作为判断量，高于阈值判为斑马线。

二、行人、红绿灯检测
通过基于coco数据集训练的Yolov5检测行人、红绿灯

三、红绿灯识别
通过CNN图片分类网络将输入图片分类。定义网络输入图片大小为60*32*3，经过两层卷积和最大池化后连接两个全连接层，再进行log_softmax运算得到三个标签对应的概率。

```python
class Net(nn.Module):
    def __init__(self):
        super(Net,self).__init__()
        # 1 input image channel 6 output channels 5*5 square convolution
        #kernal
        # 60*32*3->56*28*6
        self.conv1=nn.Conv2d(3,6,5)

        self.conv2=nn.Conv2d(6,16,5)
        # an affine operation y=wx+b
        self.fc1=nn.Linear(12*5*16,120)
        self.fc2=nn.Linear(120,84)
        self.fc3=nn.Linear(84,3)

    def forward(self,x):
        # Max pooling over a (2,2) window
        # 56*28*6->28*14*6
        x=F.max_pool2d(F.relu(self.conv1(x)),(2,2))
        # 24*10*16->12*5*16
        x=F.max_pool2d(F.relu(self.conv2(x)),2)
        x=x.view(-1,16*5*12)
        x=F.relu(self.fc1(x))
        x=F.relu(self.fc2(x))
        x=self.fc3(x)
        x=F.log_softmax(x,dim=1)
        return x

```
四、闯红灯识别
当红灯为绿灯时（路口视角，相对于行人是红灯，计算行人的边界框和人行横道边界框的位置关系，当二者距离大于某个阈值，则说明该行人闯红灯。当红灯为红灯时，检测车辆行驶的方向，如果朝红灯所在的方向行驶，则说明该车辆闯红灯。

## 2. 车辆测速
通过目标跟踪，我们可以获取视频中不同帧的目标序列，通过建立视频中的像素和现实生活中距离的对应方式，我们可以获取一段实际内车辆的位移，从而求出车辆的速度。我们保存当前帧前8帧的目标跟踪位置和当前目标跟踪框的位置，计算像素点的差值，再通过计算像素点的差值和现实位移的比例关系，测算8帧的位移，从而得到速度。利用计算的结果，我们可以进行超速的监测。
## 3. 车辆逆行检测、逆行识别
通过建立前8帧和当前帧中心点的对应关系，我们可以推算出车辆的行驶方向向量，并将其用箭头展示出来。
## 4. 基于虚线圈法的车流量统计
基于虚拟线圈的车流量统计算法原理与交通道路上的常见的传统的物理线圈类似，由于物理线圈需要埋设在路面之下，因此会有安装、维护费用高，造成路面破坏等问题，而采用基于视频的虚拟线圈的车辆计数方法完全避免了以上问题，且可以针对多个感兴趣区域进行检测。

虚拟线圈车辆计数法的原理是在采集到的交通流视频中，在需要进行车辆计数的道路或路段上设置一条或一条以上的检测线对通过车辆进行检测，从而完成计数工作。检测线的设置原则一般是在检测车道上设置一条垂直于车道线，居中的虚拟线段，通过判断其与通过车辆的相对位置的变化，完成车流量统计的工作。如下图所示，绿色的线就是虚拟检测线：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8a3b37165ed94d118c15826463fb726d.png)

在该项目中我们进行检测的方法是，计算前后两帧图像的车辆检测框的中心点连线，若该连线与检测线相交，则计数加一，否则计数不变。

假设有两个线段，AB、CD，如果线段CD的两个端点C和D，与另一条线段的一个端点（A或B，只能是其中一个）连成的向量，与向量AB做叉乘，若结果异号，表示C和D分别在直线AB的两边，若结果同号，则表示CD两点都在AB的一边，则肯定不相交。由于本项目设计的虚拟线圈平行于x轴，所以只要判断y坐标的变换即可。

获取前8帧和当前帧的y坐标y1和y2，虚拟线圈坐标为y0，若y1<y0，y2>y0，则进入流量加1，若y1>y0，y2<y0，则出流量+1，同时将结果保存在set容器中，防止重复计数。

```python
if self.carLocation1[id][3]<=self.virtureLine[1] and y2>=self.virtureLine[1]:
    self.inCar.add(id)
elif self.carLocation1[id][1]>=self.virtureLine[1] and y1<=self.virtureLine[1]:
    self.outCar.add(id) 
```
## 5. 车辆型号判断
采用斯坦福车辆数据集：
[http://ai.stanford.edu/~jkrause/cars/car_dataset.html](http://ai.stanford.edu/~jkrause/cars/car_dataset.html)

```python
    model_ft = models.resnet34(pretrained=True)
    num_ftrs = model_ft.fc.in_features
    model_ft.fc = nn.Linear(num_ftrs, 196)
```
选用预训练好的resnet34分类网络，替换最后一层的输出类别，通过冻结前面层的数据进行迁移学习，最后得到车辆的型号信息。
## 6. 车牌检测与识别
（1）MTCNN
MTCNN 一开始主要是拿来做人脸识别的。MTCNN人脸检测是2016年的论文提出来的，MTCNN的“MT”是指多任务学习(Multi-Task)，在同一个任务中同时学习”识别人脸“、”边框回归“、”人脸关键点识别“。

（2）LPRNet
LPRNet全程就叫做License Plate Recognition via Deep Neural Networks（基于深层神经网络的车牌识别）。LPRNet由轻量级的卷积神经网络组成，所以它可以采用端到端的方法来进行训练。据我们所知，LPRNet是第一个没有采用RNNs的实时车牌识别系统。因此，LPRNet算法可以为LPR创建嵌入式部署的解决方案，即便是在具有较高挑战性的中文车牌识别上。

---
# 五、资源获取(可提供整套代码、测试视频、详细说明文档、相关PPT等)
可提供YOLOv5/v7/v8/v9/v10/v11各种版本代码、训练好的权重、测试视频、详细说明文档等，代码有详细注释。包全程指导，任何问题都可以随时问我。不过有时我太忙，可能不会及时回复消息，看到了肯定回你哈。**特别的，项目包含我们写的三个word文档：概要设计文档.docx、详细设计文档.docx、需求规格使用说明书.docx，和一个PPT：基于计算机视觉的交通场景智能应用.pptx，可用于开题，中期，最终答辩，论文，课设报告、软件说明等参考。**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/63f40d8f693d4ef79accccae1cef108c.png)

**详细设计文档（共76页，13822字）。**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2824887e9f5f4672a59aa447bf3c4772.png)

**概要设计文档（共10页，1424字）、需求规格使用说明书（共15页，1831字）、基于计算机视觉的多功能智能交通系统PPT（31页）。**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/1e5361283dae4cd78dd2c76dd254837e.png)

**资源获取：**
```python
获取整套代码、测试视频、训练好的权重和说明文档(有偿)
上交硕士，技术够硬，也可以指导深度学习毕设、大作业等。
--------------->qq------------
           3582584734
------------------------------
```
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c7e9309a04bd6f22ae3f1138149f65ea.png)
