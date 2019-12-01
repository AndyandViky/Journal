# 使用torchtext将文本转为ID表示

------------------------

### 此文详细介绍了如何使用torchtext加载文本数据并且转为由ID表示的数据。
-----------
#### 概览图：
![process](https://img-blog.csdnimg.cn/20191201194042861.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)
### torchtext 加载文本数据

    def get_dataset(path, text_field, label_field):

        train, dev = data.TabularDataset.splits(
            # 支持 json, dict, csv, tsv 文件格式
            path=path, format='csv', skip_header=True,
            train='train.csv', validation='test.csv',
            fields=[
                ('index', None),
                ('label', label_field),
                ('text', text_field)
            ]
        )
        return train, dev

### 使用torchtext 进行数据预处理

    def word_cut(text):
        # 此处传入的是句子
        # 去除异常符号，数字等操作可以在分词之前操作
        return [word for word in text.split(' ')]
    text_field.tokenize = word_cut # 文本分词，可以使用其他分词技术先进行数据预处理之后再加载

    def preprocess(text):
        # 去除停止词，同义词替换等操作
        pass
    text_field.preprocessing  = preprocess # 预处理程序，tokenize之后numericalizing之前执行

    def postprocess(text):
        pass
    text_field.postprocessing  = postprocess # 该函数在numericalizing之后但数据类型未转成tensor之前

### 构建词汇表

    # 可以选择根据当前数据进行词汇表的构建；也可以根据预训练好的模型进行词汇表的构建（第二种效果更好）
    # 预训练模型一般非常大，直接下载他人训练好的即可，例如ELMo模型
    # 构建词汇表后，word将全部由ID替代
    text_field.build_vocab(train_dataset, dev_dataset)
    if args.static and args.pretrained_name and args.pretrained_path:
        vectors = Vectors(name=args.pretrained_name, cache=args.pretrained_path)
        text_field.build_vocab(train_dataset, dev_dataset, vectors=vectors)
    else:
        text_field.build_vocab(train_dataset, dev_dataset)

### batch分割

    train_iter, dev_iter = data.Iterator.splits(
        (train_dataset, dev_dataset),
        batch_sizes=(batch_size, len(dev_dataset)),
        sort_key=lambda x: len(x.text),
        **kwargs)