# feature_selection_algorithm
## Some improved feature selection algorithms
* feature selection RFE
  * 由sklearn原始的代码改进而来：[sklearn.feature_selection.RFE](https://github.com/scikit-learn/scikit-learn/blob/7813f7efb/sklearn/feature_selection/rfe.py#L36 "悬停显示")
  * 递归特征消除数量由当前特征数计算得到，以改善运行速度与递归消除的质量
  * 特征数量小于需求特征数量的2倍时，每次迭代特征消除数量为1，以增加稳定性
 
* feature selection Lasso
  * 原始的lasso算法通过手动改变参数alpha值来进行特征选择
  * 加入改进策略（类似于二分查找。详情请阅读代码），可以通过指定保留特征数量，自主选择alpha值
  * 加入了迭代次数限制，默认100，防止运行时间过长
  * 加入特征数量浮动限制，默认为2，即选择特征数量为10时，最终结果【11，13】都属正常
  * 上述两个限制都可通过参数修改
  * `example`：
    ```python
    from sklearn.datasets import make_friedman1
    from feature_selection_Lasso import lasso
    x, y = make_friedman1(n_samples=50, n_features=1000, random_state=0)
    print(x.shape)
    selector = lasso(x,y,feature_num=10,swim=2,iteration=100)
    selector.run()
    ```
* feature selection ReliefF
  * 主要针对运行速度改进，改进后的ReliefF运行速度非常快
  * `example`：
    ```python
    from sklearn.datasets.samples_generator import make_classification
    x, y = make_classification(n_samples=1000, n_features=1000, random_state=0,n_classes=2)
    print(x.shape)
    selector = ReliefF(3) #
    selector.fit(x,y)
    from sklearn.svm import SVC
    from sklearn.model_selection import train_test_split
    svc = SVC(kernel='rbf')
    x_ = x[:,selector.top_features[:2]]
    x_train, x_test, y_train, y_test = train_test_split(x_, y, test_size=.3)
    model = svc.fit(x_train,y_train)
    score = model.score(x_test,y_test)
