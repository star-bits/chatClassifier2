# chatClassifier2
[chatClassifier](https://github.com/star-bits/chatClassifier), but with [tiktoken](https://github.com/openai/tiktoken) tokenizer

| Model                         | Validation Accuracy | Test Accuracy |
|-------------------------------|---------------------|---------------|
| chatClassifier (SentencePiece)| 0.25                | 0.29          |
| chatClassifier2 (tiktoken)    | 0.17                | 0.21          |

## Tokenization example
```
제가 사랑의 매로 감싸드릴수 있는데
sentencepiece: ['▁제가', '▁사랑', '의', '▁매', '로', '▁감', '싸', '드릴', '수', '▁있는데']
sentencepiece: [203, 1235, 7929, 175, 7892, 47, 8139, 2282, 7916, 526]
tiktoken: ['제', '가', ' 사', '�', '�', '의', ' �', '�', '로', ' �', '�', '�', '�', '드', '�', '�', '수', ' 있는', '�', '��']
tiktoken: [38187, 20565, 33229, 39519, 239, 21028, 34085, 97, 17835, 17196, 238, 22254, 116, 30446, 20701, 112, 24140, 65621, 167, 43002]
```

## Inference example

Tokenization done with SentencePiece, padding with `pad_sequences` from tensorflow, with `pad_maxlen` set to 25:
```
존예디컵 여름 풀파티 하기 딱이긴 하겠네요
[[   0    0    0    0    0    0    0    0    0    0    0    0    0    0
     0    0 1560 3002  628 4522 3198  485 1187   19  403]]
<unk><unk><unk><unk><unk><unk><unk><unk><unk><unk><unk><unk><unk><unk><unk><unk>▁존예디컵▁여름▁풀파티▁하기▁딱이긴▁하겠네요
1/1 [==============================] - 0s 19ms/step
1) 爱德华/잠실르엘판매중/25: 54.90%
2) Jinyoung. woo: 7.72%
3) 정대만: 4.46%
4) 심언니: 4.13%
5) kich: 2.99%
```

Tokenization done with tiktoken, padding with `pad_sequences` from tensorflow, with `pad_maxlen` set to 50:
```
존예디컵 여름 풀파티 하기 딱이긴 하겠네요
[[    0     0     0     0     0     0     0     0     0     0     0     0
      0     0     0     0     0     0     0   168 91657 36092   230 90335
    168   119   113 84618 64254 10997   240   222 67218   234   169   233
    108 55000 21121 77597   109 13094 18202   112 55000 28740   254 76242
     97 36811]]
!!!!!!!!!!!!!!!!!!!존예디컵 여름 풀파티 하기 딱이긴 하겠네요
1/1 [==============================] - 0s 20ms/step
1) 爱德华/잠실르엘판매중/25: 14.20%
2) 박찬홍/ 주생아/엉아들 잘부탁해~: 8.95%
3) 랄프: 6.44%
4) 이상덕/울면서기도하는남쟈: 5.57%
5) kich: 4.81%
```

## Finishing thoughts
Using SentencePiece for tokenization, the average token length in this specific dataset is 6.09, while with tiktoken, it is 14.30. Consequently, the `max_padlen` for the dataset processed with tiktoken is set to double that of SentencePiece. 

It's important to highlight that token ID 0 corresponds to `<unk>` in SentencePiece and `!` in tiktoken. tiktoken's unconventional approach of not assigning ID 0 to `<unk>` may have affected accuracy. (The `pad_maxlen` function allows altering the padding number, but I could not find the id for `<unk>` token, let alone the existence of one.) 

However, the primary cause of tiktoken's lower performance likely comes from SentencePiece employing BPE to generate tokens from the dataset, while tiktoken merely identifies corresponding tokens using a pre-existing lookup table.

First 20 tokens from SentencePiece:
```
['<unk>', '<s>', '</s>', '..', '▁이', 'ᄏᄏ', '▁사', '니다', '▁아', '▁그', '티콘', '▁이모', '▁이모티콘', '▁사진', '...', '▁ᄏᄏ', '▁선', '▁안', '▁하']
```

First 20 tokens from tiktoken:
```
['!', '"', '#', '$', '%', '&', "'", '(', ')', '*', '+', ',', '-', '.', '/', '0', '1', '2', '3', '4']
```
