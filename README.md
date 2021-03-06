# sentiment_analysis
一个基于LSTM，TextCNN，fasttext实现的购物网站评论情感分析，使用tf_serving和flask部署模型

##### LSTM

&emsp; 训练模型得到pb文件
```
python data_preprocessing.py
python pre_embedding.py
python main.py
```

&emsp; 启动tf_serving服务
```
docker run -t --rm -p 8501:8501 -v "/****/****/sentiment_analysis/tfserving:/models/sentiment_analysis/" -e MODEL_NAME=sentiment_analysis tensorflow/serving
```

&emsp; HTTP接口测试
```
curl -d '{"instances": [{"input_x": [122, 91, 342, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], "dropout_keep_prob": 1}], "signature_name":"my_signature"}' -X POST http://localhost:8501/v1/models/sentiment_analysis:predict
{
    "predictions": [[0.0103441, 0.989656]
    ]
```

&emsp; 执行flask脚本
```
python flask_demo.py
```

![](/lstm/img/web.png)

&emsp; text_data 包含正样本 comments_positive.txt 负样本 comments_negative.txt
```
漂亮，连适配器都是小巧精致的，也是白色，很好看~屏幕亮度挺高，晚上开到最亮刺眼~ 续航时间没有那么长，大概5个小时左右，不过也够用了。
环境地理位置很好，交通方便。房间舒适，服务很好
"物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,物流速度太慢了,"
宾馆服务非常好，环境也很好，但在宾馆吃饭太贵了，地角很偏，交通很不方便。
```
```
初五住的，这么冷的天没有暖气，没有热水。携程预定前台说没有收到。总之感觉很业余。
预订是携程小姐推荐的，怎么说，一个字，烂 ，两个字，真烂，三个字，实在烂。典型的政府国营单位的痕迹，根本不懂什么叫服务，房间既小又破，三星都勉强。花400多买了瓶红酒，馊的！
没什么意思。后悔中。可以不用看了
```

&emsp; 通过检查点或者pb文件脚本调用模型进行预测
```
python predict.py  --pb 0
请输入:你好
predict values: [0.83590496 0.16409501]
正向
请输入:喜欢你
predict values: [0.8662184  0.13378157]
正向
请输入:不喜欢
predict values: [0.02625261 0.97374743]
负向
请输入:很差
predict values: [0.00788898 0.99211097]
负向
请输入:exit
```
```
python predict.py  --pb 1
请输入:你好
predict values: [0.83590496 0.16409501]
正向
请输入:喜欢你
predict values: [0.8662184  0.13378157]
正向
请输入:不喜欢
predict values: [0.02625261 0.97374743]
负向
请输入:很差
predict values: [0.00788898 0.99211097]
负向
请输入:exit
```

&emsp; tensorboard
```
tensorboard --logdir ./logs/summary
```

![](/lstm/img/loss.png)

![](/lstm/img/acc.png)


##### textcnn

&emsp; 训练
```
python train.py
```

&emsp; 预测
```
python predict.py --text "很差"
predict values: [0.00267654 0.99732345]
负向
```

##### fasttext

&emsp; 执行脚本

```
python train.py

-------------模型测试-------------
accuracy: 0.903
precision: 0.886
recall: 0.915
-------------混淆矩阵-------------
[[1015  123]
 [  89  954]]
------------预览预测结果------------
文本: 蒙牛 有 问题 伊利 有 问题 燕塘 有 问题 营养 快线 有 问题 奶粉 有 问题 甘 还 可以 饮 咩 呢	
实际: __label__0	
预测: __label__0

文本: 自己 实用 最 重要 照相 ． Ｍ Ｐ ３ 播放 ． ６ ０ Ｍ 内存 ． 手感 好 ．	
实际: __label__1	
预测: __label__1

文本: " 房间 可以 特别 的 套房 非常 不错 就是 餐厅 太脏 了 早餐 搞 得 没 胃口 "	
实际: __label__1	
预测: __label__1
```
