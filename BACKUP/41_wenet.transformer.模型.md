# [wenet transformer 模型](https://github.com/wjwever/gitblog/issues/41)

```python
ASRModel(
  (encoder): TransformerEncoder(
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
    (after_norm): LayerNorm((256,), eps=1e-05, elementwise_affine=True)
    (encoders): ModuleList(
      (0-11): 12 x TransformerEncoderLayer(
        (self_attn): MultiHeadedAttention(
          (linear_q): Linear(in_features=256, out_features=256, bias=True)
          (linear_k): Linear(in_features=256, out_features=256, bias=True)
          (linear_v): Linear(in_features=256, out_features=256, bias=True)
          (linear_out): Linear(in_features=256, out_features=256, bias=True)
          (dropout): Dropout(p=0.0, inplace=False)
        )
        (feed_forward): PositionwiseFeedForward(
          (w_1): Linear(in_features=256, out_features=2048, bias=True)
          (activation): ReLU()
          (dropout): Dropout(p=0.1, inplace=False)
          (w_2): Linear(in_features=2048, out_features=256, bias=True)
        )
        (norm1): LayerNorm((256,), eps=1e-05, elementwise_affine=True)
        (norm2): LayerNorm((256,), eps=1e-05, elementwise_affine=True)
        (dropout): Dropout(p=0.1, inplace=False)
      )
    )
  )
  (decoder): TransformerDecoder(
    (embed): Sequential(
      (0): Embedding(57, 256)
      (1): PositionalEncoding(
        (dropout): Dropout(p=0.1, inplace=False)
      )
    )
    (after_norm): LayerNorm((256,), eps=1e-05, elementwise_affine=True)
    (output_layer): Linear(in_features=256, out_features=57, bias=True)
    (decoders): ModuleList(
      (0-5): 6 x DecoderLayer(
        (self_attn): MultiHeadedAttention(
          (linear_q): Linear(in_features=256, out_features=256, bias=True)
          (linear_k): Linear(in_features=256, out_features=256, bias=True)
          (linear_v): Linear(in_features=256, out_features=256, bias=True)
          (linear_out): Linear(in_features=256, out_features=256, bias=True)
          (dropout): Dropout(p=0.0, inplace=False)
        )
        (src_attn): MultiHeadedAttention(
          (linear_q): Linear(in_features=256, out_features=256, bias=True)
          (linear_k): Linear(in_features=256, out_features=256, bias=True)
          (linear_v): Linear(in_features=256, out_features=256, bias=True)
          (linear_out): Linear(in_features=256, out_features=256, bias=True)
          (dropout): Dropout(p=0.0, inplace=False)
        )
        (feed_forward): PositionwiseFeedForward(
          (w_1): Linear(in_features=256, out_features=2048, bias=True)
          (activation): ReLU()
          (dropout): Dropout(p=0.1, inplace=False)
          (w_2): Linear(in_features=2048, out_features=256, bias=True)
        )
        (norm1): LayerNorm((256,), eps=1e-05, elementwise_affine=True)
        (norm2): LayerNorm((256,), eps=1e-05, elementwise_affine=True)
        (norm3): LayerNorm((256,), eps=1e-05, elementwise_affine=True)
        (dropout): Dropout(p=0.1, inplace=False)
      )
    )
  )
  (ctc): CTC(
    (ctc_lo): Linear(in_features=256, out_features=57, bias=True)
    (ctc_loss): CTCLoss()
  )
  (criterion_att): LabelSmoothingLoss(
    (criterion): KLDivLoss()
  )
)
```
上面是wenet的transformer网络结构，观察到可以发现有几个重要组成部分，后面会仔细研究这几个层的作用
* (embed): Conv2dSubsampling4  这是一个由2个卷积层加一个位置编码成构成
* 12 x encoder，12层transformer encoder层
* 6 x decoder，6层的transformer decoder层
* loss层，CTC loss + LabelSmoothingLoss
## Conv2dSubsampling4 模块
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
* x送入卷积层的特征x的形状为(12, 1, 397, 80)，12表示batch，1为输入channel数，这个为了配置卷积层使用的，397表示时间维度frame的数量，80表示频率维度
* x卷积完后形状为(12, 256, 98, 19)，12表示batch，256输出通道数，98 时间维度，19 频率维度
* x 形状变换为（12， 98， 256 * 19），在进行线性变换为 （1298, 256）
* x 位置编码，shape为 (12, 98, 256)， pos_emb 形状为 = [1, 98, 256 ]
* mask 在时间T维度，做两次采样，为（12， 1，98）
## MultiHeadedAttention 模块
堆了12层encoder模块
```python
        self.encoders = torch.nn.ModuleList([
            TransformerEncoderLayer(
                output_size,
                MultiHeadedAttention(attention_heads, output_size,
                                     attention_dropout_rate),
                PositionwiseFeedForward(output_size, linear_units,
                                        dropout_rate), dropout_rate,
                normalize_before) for _ in range(num_blocks)
        ])
```
MultiHeadAttention  attention_heads = 4 , output_size  = 256，    attention_dropout_rate: 0.0
forward_qkv函数
```python
        n_batch = query.size(0)
        q = self.linear_q(query).view(n_batch, -1, self.h, self.d_k)
        k = self.linear_k(key).view(n_batch, -1, self.h, self.d_k)
        v = self.linear_v(value).view(n_batch, -1, self.h, self.d_k)
        q = q.transpose(1, 2)  # (batch, head, time1, d_k)
        k = k.transpose(1, 2)  # (batch, head, time2, d_k)
        v = v.transpose(1, 2)  # (batch, head, time2, d_k)
```
query，key，value都是上面的x，shape为(12, 98, 256)，先进行了 256到256的线性变换，接下来做了一个最后一个维度256变成（4， 64）操作，x的shape变为（12，98,4,64）最后进行transpose操作 维度变为（12,4,98,64）
```python
        if cache.size(0) > 0:
            key_cache, value_cache = torch.split(
                cache, cache.size(-1) // 2, dim=-1)
            k = torch.cat([key_cache, k], dim=2)
            v = torch.cat([value_cache, v], dim=2)
        # NOTE(xcsong): We do cache slicing in encoder.forward_chunk, since it's
        #   non-trivial to calculate `next_cache_start` here.
        new_cache = torch.cat((k, v), dim=-1)

        scores = torch.matmul(q, k.transpose(-2, -1)) / math.sqrt(self.d_k)
        return self.forward_attention(v, scores, mask), new_cache
```
cache 相关逻辑暂时忽略，
scores = torch.matmul(q, k.transpose(-2, -1)) / math.sqrt(self.d_k)
这句计算q 和 k的相关系数，shape为 （12,4,98, 98）， self.d_k = 64
mask 保持不变 （12， 1， 98）

forward_attention函数
```python
        n_batch = value.size(0)
        # NOTE(xcsong): When will `if mask.size(2) > 0` be True?
        #   1. onnx(16/4) [WHY? Because we feed real cache & real mask for the
        #           1st chunk to ease the onnx export.]
        #   2. pytorch training
        if mask.size(2) > 0 :  # time2 > 0
            mask = mask.unsqueeze(1).eq(0)  # (batch, 1, *, time2)
            # For last chunk, time2 might be larger than scores.size(-1)
            mask = mask[:, :, :, :scores.size(-1)]  # (batch, 1, *, time2)
            scores = scores.masked_fill(mask, -float('inf'))
            attn = torch.softmax(scores, dim=-1).masked_fill(
                mask, 0.0)  # (batch, head, time1, time2)
        # NOTE(xcsong): When will `if mask.size(2) > 0` be False?
        #   1. onnx(16/-1, -1/-1, 16/0)
        #   2. jit (16/-1, -1/-1, 16/0, 16/4)
        else:
            attn = torch.softmax(scores, dim=-1)  # (batch, head, time1, time2)
```
先计算一下batch的数量，12
判断mask.size(2) > 0, 答案是true，进入if分支
mask变为（12,1,1,98），然后eq(0) 相当于取反操作
scores = scores.masked_fill(mask, -float('inf'))，score在mask为1的地方赋值为负无穷大
attn = torch.softmax(scores, dim=-1).masked_fill(mask, 0.0) ，scores最后一维取softmax，并把mask为1的地方赋值为0
attn shape应该还是（12,4,98,98），但是每个head都考虑了mask

```python
        p_attn = self.dropout(attn)
        x = torch.matmul(p_attn, value)  # (batch, head, time1, d_k)
        x = (x.transpose(1, 2).contiguous().view(n_batch, -1,
                                                 self.h * self.d_k)
             )  # (batch, time1, d_model)

        return self.linear_out(x)  # (batch, time1, d_model)
```
attn进行dropout操作，
x = torch.matmul(p_attn, value)  # (batch, head, time1, d_k)  # p_attn = (12,4,98,98)  * value = (12, 4, 98, 64) ,   attention操作，shape为（12,4,98,64）
x 进行transpose操作 （12,98,4,64），再变换成（12,98,256），和送进去的shape一样，但是进行了attention操作
再做一个linear线性层变换，维度还是（12,98,256）

https://zhuanlan.zhihu.com/p/338817680