# [降采样模块Conv2dSubsampling4](https://github.com/wjwever/gitblog/issues/42)

## 模型结构
```python  
  (embed): Conv2dSubsampling4(
      (conv): Sequential(
        (0): Conv2d(1, 256, kernel_size=(3, 3), stride=(2, 2))
        (1): ReLU()
        (2): Conv2d(256, 256, kernel_size=(3, 3), stride=(2, 2))
        (3): ReLU()
      )
      (out): Sequential(
        (0): Linear(in_features=4864, out_features=256, bias=True)
      )
      (pos_enc): PositionalEncoding(
        (dropout): Dropout(p=0.1, inplace=False)
      )
    )
```
* conv 卷积层
* out 线性层
* pos_enc 位置编码层