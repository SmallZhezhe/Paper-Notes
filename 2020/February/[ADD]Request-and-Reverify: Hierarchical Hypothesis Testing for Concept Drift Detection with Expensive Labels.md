# HHT-CU标签的使用

只有在第二层才使用标签，第一层的时候的不确定性是这样定义的：
$$
u_t=\|y_t-P(y_t|X_t)\|_2\\
P(y_t|X_t)是由分类器得到的预测成y_t的概率，而y_t是预测的类别。
$$
而1-of-K coding schem是一种编码方式，是指将K类编码为1到K。作者在这里规定了编码方式，因为编码方式和u的值直接相关。