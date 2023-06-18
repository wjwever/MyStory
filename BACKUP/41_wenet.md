# [wenet](https://github.com/wjwever/gitblog/issues/41)

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