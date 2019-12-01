# nlp 中文数据预处理

-----------------------------------------

### 此博文详细介绍中文数据预处理的过程并配上一定量的代码作为实例

#### 数据加载（默认csv格式）

    import pandas as pd
    datas = pd.read_csv("./test.csv", header=0, index_col=0) # DataFrame
    n_datas = data.to_numpy() # ndarray 转成numpy更好处理（个人喜好）

#### 去除空行

    def delete_blank_lines(sentences):
        return [s for s in sentences if s.split()]

    no_line_datas = delete_blank_lines(n_datas)

#### 去除数字

    DIGIT_RE = re.compile(r'\d+')
    no_digit_datas = DIGIT_RE.sub('', no_line_datas)
    def delete_digit(sentences):
        return [DIGIT_RE.sub('', s) for s in sentences]

#### 判断句子形式（简单句或者复杂句）

    STOPS = ['。', '.', '?', '？', '!', '！']  # 中英文句末字符
    def is_sample_sentence(sentence):
        count = 0
        for word in sentence:
            if word in STOPS:
                count += 1
                if count > 1:
                    return False
        return True
            
#### 去除中英文标点

    from string import punctuation
    import re

    punc = punctuation + u'.,;《》？！“”‘’@#￥%…&×（）——+【】{};；●，。&～、|\s:：'
    def delete_punc(sentences):
        return [re.sub(r"[{}]+".format(punc), '', s) for s in a]

#### 去除英文（仅留汉字）

    ENGLISH_RE = re.compile(r'[a-zA-Z]+')
    def delete_e_word(sentences):
        return [ENGLISH_RE.sub('', s) for s in sentences]

#### 去除乱码和特殊符号
使用正则表达式去除相关无用符号和乱码
    
    # 该操作可以去掉所有的符号，标点和英文，由于前期可能需要标点进一步判断句子是否为简单句，所以该操作可以放到最后使用。
    SPECIAL_SYMBOL_RE = re.compile(r'[^\w\s\u4e00-\u9fa5]+')
    def delete_special_symbol(sentences):
        return [SPECIAL_SYMBOL_RE.sub('', s) for s in sentences]

#### 中文分词

    # 使用jieba
    def seg_sentences(sentences):
        cut_words = map(lambda s: list(jieba.cut(s)), sentences)
        return list(cut_words)

    # 使用pyltp分词
    def seg_sentences(sentences):
        segmentor = Segmentor()
        segmentor.load('./cws.model') # 加载分词模型参数
        seg_sents = [list(segmentor.segment(sent)) for sent in sentences]
        segmentor.release()
        return seg_sents

#### 去除停用词
    
    # 停用词列表需要自行下载
    stopwords = []
    def delete_stop_word(sentences):
        return [[word for word in s if word not in stopwords] for s in sentences]

__References__  
https://www.cnblogs.com/lookfor404/p/9784630.html  
https://blog.csdn.net/hfutdog/article/details/86495574