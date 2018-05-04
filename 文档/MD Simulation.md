# 分子动力学模拟
## 1. 准备


###  1.1 $\boldsymbol{^\triangle}$获取roughpolymer.pdb(原始粗糙的pdb文件)
- 在Material Studio中画出聚合物分子，导出**roughpolymer.pdb**文件。**注意头尾：头部结构单元CA有HA1，尾部CY有HY1。**
- 改残基名。对于f5均聚物，残基命名为头部、中间、尾部结构单元分别为DDR、DDS、DDT。
    
$\boldsymbol{^\triangle}$ *表示有些情况可能并非必须步骤。*

###  1.2 $\boldsymbol{^\triangle}$roughpolymer.pdb转换成polymer.gro
```shell
$ pdb2gmx -f polymer.pdb -o polymer.gro -p polymer.top
    > Force Field: 8 Charmm27
    > Water Model: 7 None
```
即得到**polymer.gro**、**polymer.top**两个文件，其中gro需要做得与x轴平行，top文件需要改动成itp文件供后面top文件#include。下述


### 1.3 $\boldsymbol{^\triangle}$polymer.gro改到与x轴平行
- *PASS*
得到**refined_polymer.gro**
- `$ editconf -f refined_polymer.gro -o refined_polymer.pdb`
得到**refined_polymer.pdb**

### 1.4 polymer.top改为polymer.itp、将polymer.itp加入（include）到graphene.top
- **polymer.top**打开，[ bonds ]、[ angles ]、[ dihedrals ]里面的各种常数依据**124-order.itp**文件改。**这一步可能反复出错，根据报错中声明缺少参数的行数来改。是最~~恶心~~繁复耗时的步骤。**
- 删除以下两个部分，分别在头部和尾部
    ```
    ; Include forcefield parameters
    #include "charmm27.ff/forcefield.itp"
    ```
    ```
    ; Include Position restraint file
    #ifdef POSRES
    ……
    [ molecules ]
    ; Compound        #mols
    Other               1
    ```
- **polymer.top**改名为**polymer.itp**
- 在**graphene.top**文件中加一句`#include polymer.itp`，也可能是把里面别的itp文件名改成**polymer.itp**。


### 1.5 修改pack.in文件、坐标文件打包、建盒子、修改complex.pdb的残基序号
- `$ packmol < pack.in`包含石墨烯（**graphene.pdb**）+改良的聚合物pdb文件   （**refined_polymer.pdb**），**pack.in**文件中的距离单位为$\mathring{A}$。
    得到**complex.pdb**


 
- 建盒子
    ```shell
    $ editconf -f complex.pdb -o complex.gro -c -box <X> <Y> <Z> # 其中XYZ的单位是nm。
    ```
    
- 修改**complex.pdb**的残基序号。1UNK保留不动；1DDR-nDDS-28DDT改为2DDR-nDDS-29DDT。


### 1.6 索引、固定、改posre.itp的力常数
- 索引。 
    ```shell
    $ make_ndx -f complex.gro -o new.ndx
        > 3|4|5 # 分别指的是DDR、DDS、DDT。
        > name 6 F5
    ```
- 固定。
    ```shell
    genrestr -f complex.gro -n new.ndx -o posre.itp
        > 'select a group': 2 # 选择固定石墨烯组 
    ```
- $^\triangle$**posre.itp**里面的1000改成10000。

至此我们得到了以下文件

|   文件    |   来源    |   备注    |
| - | - | - |
|   complex.gro  |  1.5 节 `editconf`
|   em1.mdp & npt1.mdp & nvt1.mdp   |   某处复制得来
|   graphene.top    |   1.4 节  |   要加上include polymer.itp
|   new.ndx   |     1.6 节 `make_ndx`   |
|   posre.itp   |   1.6 节`posre`   |   要加入到graphene.top中
|   polymer.itp |   1.4 节 删除、改名   |   要加入到graphene.top中

## 2. EM(能量最小化)
- 打包
    ```shell
    grompp -v -c complex.gro -f em1.mdp -o emXX.tpr -p graphene.top -n new..ndx
    ```
- 运行
    ```shell
    mdrun -v -s emXX.tar -deffnm emXX
    ```


## 3. NPT(等温等压系综)

[分子动力学模拟简介--知乎用户@yang元祐](https://zhuanlan.zhihu.com/p/26103312)：
> 等温等压系综(constant-pressure,constant-temperature)，简称NPT或者T-P系综。即表示具有确定的粒子数（N）、压强（P）、温度（T）。一般是在蒙特卡罗模拟中实现。其总能量(E)和系统体积(V)可能存在起伏。体系是可移动系统壁情况下的恒温热浴。特征函数是吉布斯自由能G(N,P,T)。

npt1.mdp文件中的
```
xtc_grps            = UNK WDA
energygrps          = UNK WDA
……
tc-grps             = UNK WDA   ;2 coupling group
```

组名需要按需求改掉。如组名WDA改成F5，这个组名是在`make_ndx`的时候命名的那个。
- 打包
    ```shell
    grompp -v -c emXX.gro -f npt1.mdp -o nptYY-emXX.tar -p graphene.top -n new.ndx 
    ```
  **nptYY-emXX.tar**表示em第XX次跑完的结果用来跑的第YY次npt。
- 运行 
    ```shell
    mdrun -v -s nptYY-emXX.tar -deffnm nptYY-emXX
    ```

## 4. NVT(正则系综)
[分子动力学模拟简介--知乎用户@yang元祐](https://zhuanlan.zhihu.com/p/26103312)：
>  正则系综（canonical ensemble），NVT，正则系综是蒙特卡罗方法模拟处理的典型代表。假定N个粒子处在体积为V的盒子内，将其置于温度恒为T的巨大热库中。此时，总能量（E）和系统压强（P）可能在某一平均值附近起伏变化。平衡体系为封闭系统，与大热源热接触，能量交换达到热平衡，温度相等，热库足够大，温度确定。特征函数是亥姆霍兹自由能F（N,V,T）。