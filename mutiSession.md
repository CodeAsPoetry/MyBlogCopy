---
title: 同一脚本加载多张图和多个Session
date: 2020-01-06 15:45:14
categories: 工程技术
tags: 多个Tensorflow计算流图部署
mathjax: true
---

出于业务需求，需要在一个服务中同时部署两个BERT模型，这两个模型是串行执行，并且两张计算流图的节点没有连接。最简单的实现方法就是在一个脚本中分别加载两张图，分别指定session和graph，构建对应的执行预测的前向推理操作。<!--more-->

直接上代码：

```python
def __init__(self):
    # 建立两个 graph
    self.g1 = tf.Graph()
    self.g2 = tf.Graph()

    # 为每个 graph 建创建一个 session
    self.sess1 = tf.Session(graph=self.g1)
    self.sess2 = tf.Session(graph=self.g2)

    self.bi_label_list = ['0', '1']
    self.bi_num_labels = len(self.bi_label_list)

    self.muti_label_list = [str(i) for i in range(40)]
    self.muti_num_labels = len(self.muti_label_list)

    self.configuration = cf.get_config()

    self.max_seq_len = self.configuration['max_seq_length']
    self.bi_bert_config_file = self.configuration['bert_config']
    self.muti_bert_config_file = self.configuration['bert_config']

    self.tokenizer = tokenization.FullTokenizer(
      vocab_file=self.configuration['vocab_file'], do_lower_case=True)

    self.bi_init_checkpoint = self.configuration['bi_init_checkpoint']
    self.muti_init_checkpoint = self.configuration['muti_init_checkpoint']

    self._init_session()

def _init_session(self):
    with self.sess1.as_default():
        with self.sess1.graph.as_default():
            self.graph = tf.get_default_graph()

            self.bi_input_ids_ph = tf.placeholder(
              tf.int32, [None, self.max_seq_len], name="input_ids")
            self.bi_input_mask_ph = tf.placeholder(
              tf.int32, [None, self.max_seq_len], name="input_mask")
            self.bi_segment_ids_ph = tf.placeholder(
              tf.int32, [None, self.max_seq_len], name="segment_ids")
            self.bi_labels_ph = tf.placeholder(
              tf.int32, [None, ], name="labels_ph")

            self.bi_bert_config = bi_modeling.BertConfig.from_json_file(self.bi_bert_config_file)

            _, _, self.bi_logits, self.bi_prediction = bi_create_model(
              self.bi_bert_config, False, self.bi_input_ids_ph, self.bi_input_mask_ph,
              self.bi_segment_ids_ph, self.bi_labels_ph, self.bi_num_labels, False)

            saver1 = tf.train.Saver()
            saver1.restore(self.sess1, tf.train.latest_checkpoint(self.bi_init_checkpoint))

            print('Successfully load the model_1!')

        with self.sess2.as_default():
            with self.sess2.graph.as_default():
                self.graph = tf.get_default_graph()
                self.muti_input_ids_ph = tf.placeholder(
                  tf.int32, [None, self.max_seq_len], name="input_ids")
                self.muti_input_mask_ph = tf.placeholder(
                  tf.int32, [None, self.max_seq_len], name="input_mask")
                self.muti_segment_ids_ph = tf.placeholder(
                  tf.int32, [None, self.max_seq_len], name="segment_ids")
                self.muti_labels_ph = tf.placeholder(
                  tf.int32, [None, ], name="labels_ph")

                self.muti_bert_config = muti_modeling.BertConfig.from_json_file(self.muti_bert_config_file)

                _, _, self.muti_logits, self.muti_prediction = muti_create_model(
                  self.muti_bert_config, False, self.muti_input_ids_ph, self.muti_input_mask_ph,
                  self.muti_segment_ids_ph, self.muti_labels_ph, self.muti_num_labels, False)

                saver2 = tf.train.Saver()
                saver2.restore(self.sess2, tf.train.latest_checkpoint(self.muti_init_checkpoint))

                print('Successfully load the model_2!')
```

