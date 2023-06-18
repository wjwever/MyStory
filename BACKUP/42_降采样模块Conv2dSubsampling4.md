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
主要构造为一下三层
* conv 卷积层
* out 线性层
* pos_enc 位置编码层
## 代码分析
```python
x = x.unsqueeze(1)  # (b, c=1, t, f)
print("x.shape before conv", x.shape)
x = self.conv(x)
print("x.shape after conv", x.shape)
b, c, t, f = x.size()
x = self.out(x.transpose(1, 2).contiguous().view(b, t, c * f))
x, pos_emb = self.pos_enc(x, offset)
print('x.shape after pos_enc, pos_emb.shape', x.shape, pos_emb.shape)

print('x_mask.shape', x_mask.shape, x_mask[:,:,2::2][:,:,2::2].shape)
return x, pos_emb, x_mask[:, :, 2::2][:, :, 2::2]
```
在代码中加了三行print，观察tensor的形状:
```python
x.shape before conv torch.Size([12, 1, 397, 80])
x.shape after conv torch.Size([12, 256, 98, 19])
x.shape after pos_enc, pos_emb.shape torch.Size([12, 98, 256]) torch.Size([1, 98, 256])
x_mask.shape torch.Size([12, 1, 397]) torch.Size([12, 1, 98])
```
* 送入卷积层的特征x的形状为(12, 1, 397, 80)，12表示batch，1为输入channel数，这个为了配置卷积层使用的，397表示时间维度frame的数量，80表示频率维度
* 卷积完后x的形状为(12, 256, 98, 19)，12表示batch，256输出通道数，98 时间维度，19 频率维度
* 位置编码完以后，shape为 (12, 98, 256)， pos_emb.shape= [1, 98, 256]
* mask 在时间T维度，做两次采样，保持和特征x一致