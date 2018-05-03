# 分子动力学模拟
## 1. 准备
###  获取roughpolymer.pdb(原始粗糙的pdb文件)
- 在Material Studio中画出聚合物分子，导出**roughpolymer.pdb**文件。**注意头尾：头部结构单元CA有HA1，尾部CY有HY1。**
- 改残基名。对于f5均聚物，残基命名为头部、中间、尾部结构单元分别为DDR、DDS、DDT。
###  roughpolymer.pdb转换成polymer.gro
```
$ pdb2gmx -f polymer.pdb -o polymer.gro -p polymer.top
    > Force Field: 8 Charmm27
    > Water Model: 7 None
```
即得到**polymer.gro**、**polymer.top**两个文件，其中gro需要做得与x轴平行，top文件需要改动成itp文件供后面top文件#include。下述
### polymer.gro改到与x轴平行
- *PASS*
得到**refined_polymer.gro**
- `$ editconf -f refined_polymer.gro -o refined_polymer.pdb`
得到**refined_polymer.pdb**
### polymer.top改为polymer.itp
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
- 后面将在**graphene.top**文件中加一句`#include polymer.itp`

### 修改pack.in文件、坐标文件打包
- `$ packmol < pack.in`包含石墨烯（**graphene.pdb**）+改良的聚合物pdb文件   （**refined_polymer.pdb**）
得到**complex.pdb**

### 建盒子、修改complex.pdb的残基序号
- 建盒子

- 修改**complex.pdb**的残基序号。1UNK保留不动；1DDR-nDDS-28DDT改为2DDR-nDDS-29DDT。