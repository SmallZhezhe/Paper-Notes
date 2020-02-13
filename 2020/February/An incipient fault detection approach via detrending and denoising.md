这篇文章是国防科大发的一篇关于早期故障检测的文章，由于早期的故障往往很小，会被trend和noise所遮盖，因此作者提出了detrending和denoising的方法来解决这个问题。

核心是在原有的Hotelling T²统计的基础上增加了这两个过程，并没有改进其方法。

原有方法为：
$$
T^2_{0,k+i}=r^T_{0,k+i}\Sigma_{0}^{-1}r_{0,k+i}
$$
这里
$$
r_{0,k+i}=x_{k+i}-\overline{x}
$$

$$
\overline{x}为均值，\Sigma_0为方差
$$

检验的阈值为：
$$
T_{0,\alpha}^{2}=\frac{n_x(k^2-1)}{k(k-n_x)}F_\alpha(n_x,k-n_x)
$$
这种方法受到trend和noise的影响很大。

# Detrending

核心就是把trend建模，然后减掉就好。体现在方法上，就是将x替换成了z，这里的z是模型的误差。

文中给了一些指导性的意见，比如说线性的trend可以用一阶多项式去model，然后有波动的可以用高阶多项式去model，有周期的可以用三角函数去model，但是并没有给范式。

模型误差z可能由两方面因素导致：

- 噪声e
- 错误f

这样便和本来的trend无关了。

所以新的检验方法是：
$$
r_{1,k+i}=z_{k+i}-\overline{z}\\
T_{1,k+i}^2=r^T_{1,k+i}\Sigma_1^{-1}r_{1,k+i}
$$
阈值和前面是一样的。

# Denoising

这个方法比上面的稍微复杂一点，但是限制也更多。

作者假设错误是决定性的并且连续，得到了下面的结论：
$$
r_{2,k+i}=\sum_{j=0}^{N_0-1}z_{k+i-j}\\
r_{2,k+i}\sim N(N_0f,(\sqrt{N_0}\sigma_e)^2),i\ge N_0\\
均值的以N_0增长，标准差以\sqrt{N_0}
$$
这表明，均值的增长速度会比标准差快，将误差平均会提升性能。

（没看懂是什么意思，原文如下：

> Apparently, the former grows faster than the latter when 𝑁0 > 1, which explains why the fault detection performance can be improved by averaging the residuals.

）

得到这个结论之后，作者便引入了一个线性组合，做了下面的修改：
$$
\Sigma_2=\sum_{j=0}^{N_0}\alpha_{k+i-j}^{2}\Sigma\\
r_{2,k+i}=\sum_{j=0}^{N_0-1}\alpha_{k+i-j}z_{k+i-j}
$$
并引用了一篇1999年的文章，证明了他们类似于上面的组合服从卡方分布：
$$
F_{k+i}=r_{2,k+i}^T\Sigma_2^{-1}r_{2,k+i}\sim \chi^2(n_x)
$$
但是这个时候方差也被拆开了，不方便计算。作者便统一使用上文去掉trend之后的方差进行计算了：
$$
F_{k+i}=\frac{k(k-n_x)}{n_x(k^2-1)}r_{2,k+i}^T\Sigma_2^{-1}r_{2,k+i} \sim F(n_x,k)
$$
这样便可以计算出：
$$
T_{2,k+i}^2 = r_{2,k+i}^T\Sigma_2^{-1}r_{2,k+i} =\frac{k(k-n_x)}{n_x(k^2-1)} F_{k+i}
$$
阈值和前面还是一样的。

这样就做到了去噪声。

# 算法

作者最后给出了一个流程图：

![](/home/liyunzhe/Pictures/typora_pic/image-20200209134409428.png)

并且在三个案例上进行了验证，取得了比较好的效果：

![image-20200209134522441](/home/liyunzhe/Pictures/typora_pic/image-20200209134522441.png)

# 分析

这篇文章对噪声和趋势进行了定义，然后提出了去噪声和去趋势的检测方法。在特定的场景下取得了比较好的效果。但是，也引入了很多新的参数：

- design function：这是给trend建模的方程，精确与否将直接影响效果。但是这个一般情况下应该都可以解决，文中也给了详细的指导。
- weight factors: 这个作者建议简化为0或者1，但是我觉得可能还是很难确定，最后的案例分析也没讲为什么要用这些参数。

因此，这个方法理论严谨，效果也很好，但是使用起来可能有点困难，可能需要通过试来试出一个比较好的结果来。