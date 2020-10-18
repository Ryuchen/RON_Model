# 2020 数学建模比赛 B题《汽油辛烷值建模》
> 个人结题思路记录
--- 

题目说明见 《汽油辛烷值优化建模.docx》

详细题目说明先项目 附件一到四

## 问题一：

数据处理：请参考近4年的工业数据(见附件一“325个数据样本数据.xlsx”)的预处理结果，依“样本确定方法”（附件二）对285号和313号数据样本进行预处理（原始数据见附件三“285号和313号样本原始数据.xlsx”）并将处理后的数据分别加入到附件一中相应的样本号中，供下面研究使用。

> 认真阅读《附件二：样本确定方法.docx》

个人解题思路:

### 考点：数据预处理 ====> 补全数据&修正数据

### 解题思路：

#### 前提假设：
    
+ 短时间内对于人为操作对于采样数据的影响体现不及时，采样数据仍然呈现相应的高斯分布规律

#### 解题思路：

+ 补全数据：

    - 即需要先确认 样本数据集 $\displaystyle{\mathcal{X}}_{s}$ 中 285号 $x_{s}^{(285)}$ 样本 和 313号 $x_{s}^{(313)}$ 样本 存在属性值缺失的属性集合 $\displaystyle{\mathcal{D}}_{miss}^{(285)}$ 和 $\displaystyle{\mathcal{D}}_{miss}^{(313)}$
        - 然后判断这些缺失的属性 $d_{miss} \in \displaystyle{\mathcal{D}}_{miss}$ 是否在 原始数据集 $\displaystyle{\mathcal{X}}_{r}$ 中也存在数值缺失，依据 缺失率(Missing Value Ratio) 对其进行删减 $\displaystyle{\hat{\mathcal{D}}}_{miss}$
    - 对于剩余的 缺失属性集合 $\displaystyle{\hat{\mathcal{D}}}_{miss}$ 中的缺失的属性值从 原始数据集 $\displaystyle{\mathcal{X}}_{r}$ 中以求取均值 mean($\displaystyle{\mathcal{V}_{r}}$) 的方式得到

+ 修正数据：

    - 即需要先确认 原始数据集 $\displaystyle{\mathcal{X}}_{r}$ 中 285号 $x_{s}^{(285)}$ 和 313号 $x_{s}^{(313)}$ 样本中各属性的属性值缺失情况，对于缺失情况较大的属性则不对 样本数据集 $\displaystyle{\mathcal{X}}_{s}$ 中的 属性值 $\displaystyle{\mathcal{D}}$ 进行修正
    - 将 285号 $x_{s}^{(285)}$ 和 313号 $x_{s}^{(313)}$ 样本中需要修正的属性依据 正态分布 $x \sim \displaystyle{\mathcal{N}}(\mu, \sigma)$ 假设检验，然后使用 依达拉原则(3$\sigma$) 剔除一些不在范围内的样本，对剩余的原始数据 $\displaystyle{\hat{\mathcal{X}}_{r}}$ 进行求取均值 mean($\displaystyle{\hat{\mathcal{V}}_{r}}$) 的操作

#### 相应公式：

+ 缺失率(Missing Value Ratio) = $\frac{\displaystyle{\mathbb{I}}(v_{loss}^{(i)} = 0|d_{loss}^{(i)}  \in \displaystyle{\mathcal{D}}_{loss})}{\displaystyle{\mathbb{I}}(v_{loss}^{(i)} |d_{loss}^{(i)} \in \displaystyle{\mathcal{D}}_{loss})}$ 
+ 变异系数(Coefficient of Variation) =  $\frac{\displaystyle{\text{std}}(\displaystyle{\mathcal{V}_{r})}}{\displaystyle{\text{mean}}(\displaystyle{\mathcal{V}_{r}})}$ 

#### 代码逻辑：

+ 补全数据：

    - 首先从 样本数据集 $\displaystyle{\mathcal{X}}_{s}$ 中确定样本 285号 $x_{s}^{(285)}$ 和 313号 $x_{s}^{(313)}$ 缺失的属性集合 $\displaystyle{\mathcal{D}}_{loss}^{(285)}$ 和 $\displaystyle{\mathcal{D}}_{loss}^{(313)}$
    - 然后分别对两个集合中缺失的属性 $d_{loss} \in \displaystyle{\mathcal{D}}_{loss}$ 对应到 原始数据集 $\displaystyle{\mathcal{X}}_{r}$ 中，逐一对每个缺失的属性值进行判别
    - 这里使用 缺失率(Missing Value Ratio) $r_{1}$ 对当前 缺失的属性 $d_{loss}$ 在 原始数据集 $\displaystyle{\mathcal{X}}_{r}$ 中 数据采集的状况 进行评估
        + 当比值 $ r_{1} \ge \frac{9}{10} $ 时，对当前 缺失的属性的值 $\displaystyle{\mathcal{V}_{d_{loss}}}$ 进行求均值 mean($\displaystyle{\mathcal{V}_{d_{loss}}}$) 处理；(此处求取均值之前仍然需要对原始数据进行修正，修正步骤见下述)
        + 当比值 $ r_{1} < \frac{9}{10} $ 时，对当前 缺失的属性 $d_{loss}$ 进行直接忽略处理；

+ 修正数据：

    + 对样本 $x_{s}^{(285)}$ 和 $x_{s}^{(313)}$ 进行 缺失的属性 $d_{loss}^{(285)} \in \displaystyle{\mathcal{D}}_{loss}^{(285)}$ 和 $d_{loss}^{(313)} \in \displaystyle{\mathcal{D}}_{loss}^{(313)}$ 求均值之前 先需要进行异常值处理
        - 根据拉依达准则（3$\sigma$准则）去除异常值，得到 缺失的属性 $d_{loss}^{(i)}$ 对应的过滤后的 原始属性值 集合 $\displaystyle{\hat{\mathcal{V}}_{d_{loss}}}$ 
        - 将处理后的 原始属性值集合 $\displaystyle{\hat{\mathcal{V}}_{d_{loss}}}$ 中的属性值进行求取 均值 mean($\displaystyle{\hat{\mathcal{V}}_{d_{loss}}}$) 操作，将结果加入到 $\displaystyle{\mathcal{X}}_{s}$ 中对应的 样本数据  $x_{s}^{(285)}$ 和 $x_{s}^{(313)}$ 对应的 缺失的属性 $d_{loss}^{(i)}$ 中

#### 可调参数：

+ $r_{1}$ => 缺失率(Missing Value Ratio) 

---

> 对应的处理代码脚本为 Step_1.ipynb

---