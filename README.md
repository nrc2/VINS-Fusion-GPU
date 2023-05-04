# VINS-Fusion-gpu
Este repositório é uma versão do VINS-Fusion com aceleração de GPU. Ele pode rodar em Nvidia TX2 em tempo real.
## 1. Pré-requisitos
O ambiente de software essencial é o mesmo do VINS-Fusion. Além disso, requer a versão cuda do OpenCV. (Somente teste no OpenCV 3.4.1).
## 2. Aplicação
### 2.1 Altere o caminho opencv no CMakeLists
Em /vins_estimator/CMakeLists.txt, mude Linha 20 para seu caminho.  
Em /loop_fusion/CmakeLists.txt, mude Linha 19 para seu caminho.
### 2.2 Altere os parâmetros de aceleração conforme necessário.
No arquivo de configuração, existem dois parâmetros para aceleração de gpu.  
use_gpu: 0 para off, 1 para on
use_gpu_acc_flow:  0 para off, 1 para on  
Se os recursos da sua GPU forem limitados ou você quiser usar a GPU para outros cálculos. você pode definir:
use_gpu: 1  
use_gpu_acc_flow: 0  
Se seu outro aplicativo não requer muitos recursos de GPU, recomendo que você defina 
use_gpu: 1  
use_gpu_acc_flow: 1  
De acordo com meu teste, no TX2, se você definir esses dois parâmetros como 1 ao mesmo tempo, o uso da GPU será de cerca de 20%.

# VINS-Fusion
## Um estimador de estado multissensor baseado em otimização

<img src="https://github.com/HKUST-Aerial-Robotics/VINS-Fusion/blob/master/support_files/image/vins_logo.png" width = 55% height = 55% div align=left />


O VINS-Fusion é um estimador de estado multissensor baseado em otimização, que alcança autolocalização precisa para aplicativos autônomos (drones, carros e AR/VR). VINS-Fusion é uma extensão do [VINS-Mono](https://github.com/HKUST-Aerial-Robotics/VINS-Mono), que suporta vários tipos de sensores visuais inerciais (câmera mono + IMU, câmeras estéreo + IMU, até mesmo câmeras estéreo apenas). Também mostramos um exemplo de brinquedo de fusão de VINS com GPS.

**Recursos:**
- multiple sensors support (stereo cameras / mono camera+IMU / stereo cameras+IMU)
- online spatial calibration (transformation between camera and IMU)
- online temporal calibration (time offset between camera and IMU)
- visual loop closure

<img src="https://github.com/HKUST-Aerial-Robotics/VINS-Fusion/blob/master/support_files/image/kitti_rank.png" width = 80% height = 80% />

Essa biblioteca é **top** open-sourced algoritmo stereo no [KITTI Odometry Benchmark](http://www.cvlibs.net/datasets/kitti/eval_odometry.php) (12.Jan.2019).

**Authors:** [Tong Qin](http://www.qintonguav.com), Shaozu Cao, Jie Pan, [Peiliang Li](https://peiliangli.github.io/), and [Shaojie Shen](http://www.ece.ust.hk/ece.php/profile/facultydetail/eeshaojie) from the [Aerial Robotics Group](http://uav.ust.hk/), [HKUST](https://www.ust.hk/)


**Papéis Relacionados:** (os papéis não são exatamente iguais ao código)
* **A General Optimization-based Framework for Local Odometry Estimation with Multiple Sensors**, Tong Qin, Jie Pan, Shaozu Cao, Shaojie Shen, aiXiv [pdf](https://arxiv.org/abs/1901.03638) 

* **A General Optimization-based Framework for Global Pose Estimation with Multiple Sensors**, Tong Qin, Shaozu Cao, Jie Pan, Shaojie Shen, aiXiv [pdf](https://arxiv.org/abs/1901.03642) 

* **Online Temporal Calibration for Monocular Visual-Inertial Systems**, Tong Qin, Shaojie Shen, IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS, 2018), **best student paper award** [pdf](https://ieeexplore.ieee.org/abstract/document/8593603)

* **VINS-Mono: A Robust and Versatile Monocular Visual-Inertial State Estimator**, Tong Qin, Peiliang Li, Shaojie Shen, IEEE Transactions on Robotics [pdf](https://ieeexplore.ieee.org/document/8421746/?arnumber=8421746&source=authoralert) 


*Uso o VINS-Fusion para pesquisa acadêmica, citei seus artigos relacionados. [bib](https://github.com/HKUST-Aerial-Robotics/VINS-Fusion/blob/master/support_files/paper_bib.txt)*

## 1. Pré-requisitos
### 1.1 **Ubuntu** and **ROS**
Ubuntu 64-bit 16.04 or 18.04.
ROS Kinetic or Melodic. [ROS Installation](http://wiki.ros.org/ROS/Installation)


### 1.2. **Ceres Solver**
Follow [Ceres Installation](http://ceres-solver.org/installation.html).


## 2. Build VINS-Fusion
Clone o repositório e catkin_make:
```
    cd ~/catkin_ws/src
    git clone https://github.com/HKUST-Aerial-Robotics/VINS-Fusion.git
    cd ../
    catkin_make
    source ~/catkin_ws/devel/setup.bash
```
(se esse passo falhar, tente achar outro computador com um sistema limpo ou reinstale Ubuntu e ROS)

## 3. Exemplo EuRoC
Download [EuRoC MAV Dataset](http://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets) to YOUR_DATASET_FOLDER. Pegue o MH_01 por exemplo, você pode executar o VINS-Fusion com três tipos de sensores (câmera monocular + IMU, câmeras estéreo + IMU e câmeras estéreo), aqui testei a câmera monocular + IMU.
Abra quatro terminais, execute vins odometry, fechamento de loop visual (opcional), rviz e reproduza o arquivo bag, respectivamente.
O caminho verde é a odometria VIO; o caminho vermelho é a odometria sob fechamento do loop visual.

### 3.1 Monocular camera + IMU

```
    roslaunch vins vins_rviz.launch
    rosrun vins vins_node ~/catkin_ws/src/VINS-Fusion/config/euroc/euroc_mono_imu_config.yaml 
    (optional) rosrun loop_fusion loop_fusion_node ~/catkin_ws/src/VINS-Fusion/config/euroc/euroc_mono_imu_config.yaml 
    rosbag play YOUR_DATASET_FOLDER/MH_01_easy.bag
```

<img src="https://github.com/HKUST-Aerial-Robotics/VINS-Fusion/blob/master/support_files/image/euroc.gif" width = 430 height = 240 />

## 4. Rode com seus dispositivos
O VIO não é apenas um algoritmo de software, ele depende fortemente da qualidade do hardware. Para iniciantes, recomendamos que você execute o VIO com equipamento profissional, que contém câmeras de obturador global e sincronização de hardware.

### 4.1 Arquivo de configuração
Escreva um arquivo de configuração para o seu dispositivo. Você pode pegar os arquivos de configuração do EuRoC e KITTI como exemplo.

### 4.2 Calibração da câmera
O VINS-Fusion suporta vários modelos de câmera (pinhole, mei, equidistante). Você pode usar [modelo da câmera](https://github.com/hengli/camodocal) para calibrar suas câmeras. Colocamos alguns dados de exemplo em /camera_models/calibrationdata para informar como calibrar.
```
cd ~/catkin_ws/src/VINS-Fusion/camera_models/camera_calib_example/
rosrun camera_models Calibrations -w 12 -h 8 -s 80 -i calibrationdata --camera-model pinhole
```


## 7. Reconhecimentos
Eles usaram [ceres solver](http://ceres-solver.org/) para otimização não linear e [DBoW2](https://github.com/dorian3d/DBoW2) para detecção de loop, um [modelo de câmera] genérico (https://github.com/hengli/camodocal) e [GeographicLib](https://geographiclib.sourceforge.io/).

## 8. Licensa
O código-fonte é liberado sob a licença [GPLv3](http://www.gnu.org/licenses/).

Ainda estão trabalhando para melhorar a confiabilidade do código. Para qualquer problema técnico, entre em contato com Tong Qin <qintonguavATgmail.com>.

Para consultas comerciais, entre em contato com Shaojie Shen <eeshaojieATust.hk>.
