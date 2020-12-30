# 介绍

```
基于lucene(一个索引库，一个文档库)的分布式全文检索系统，跟solr一样

自己的生态系统
ELK ElasticSearch(全文检索) Logstash（数据采集） Kibana（报表）
```

## 查看存储结构的工具

http://github.com/DmitryKey/luke/releases

## 引入ik分词器

```
智能进行关键字的拆分

收录的字典是2012年的

比如输入大数据 ==》 拆分成 “大数 ” + “数据” 2012年的时候还没有收录大数据这个整体词
```

![image-20201120143205592](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201120143205592.png)

```
		<!-- https://mvnrepository.com/artifact/com.janeluo/ikanalyzer -->
		<!--引入ik分词器-->
		<dependency>
			<groupId>com.janeluo</groupId>
			<artifactId>ikanalyzer</artifactId>
			<version>2012_u6</version>
		</dependency>

```

### IK配置

```
默认的IK分词器支持的是lucene4.7.2版本的，我们需要重新配置来使它兼容

使用的时候，直接new MyIkAnalyzer()即可
```



```
package com.cbx.es;

import org.apache.lucene.analysis.Analyzer;
import org.apache.lucene.util.IOUtils;

import java.io.Reader;
import java.io.StringReader;

/**
 * @Author:cbx
 * @Date:2020/11/20/14:57
 */
public class MyIkAnalyzer extends Analyzer{
    @Override
    protected TokenStreamComponents createComponents(String arg0) {
        Reader reader=null;
        try{
            reader=new StringReader(arg0);
            MyIKTokenizer it = new MyIKTokenizer(reader);
            return new Analyzer.TokenStreamComponents(it);
        }finally {
            IOUtils.closeWhileHandlingException(reader);
        }
    }

}

```



```
package com.cbx.es;

import org.apache.lucene.analysis.Tokenizer;
import org.apache.lucene.analysis.tokenattributes.CharTermAttribute;
import org.apache.lucene.analysis.tokenattributes.OffsetAttribute;
import org.apache.lucene.analysis.tokenattributes.TypeAttribute;
import org.wltea.analyzer.core.IKSegmenter;
import org.wltea.analyzer.core.Lexeme;

import java.io.IOException;
import java.io.Reader;

/**
 * @Author:cbx
 * @Date:2020/11/20/14:56
 */
public final class MyIKTokenizer  extends Tokenizer {
    // IK分词器实现
    private IKSegmenter _IKImplement;

    // 词元文本属性
    private final CharTermAttribute termAtt;
    // 词元位移属性
    private final OffsetAttribute offsetAtt;
    // 词元分类属性（该属性分类参考org.wltea.analyzer.core.Lexeme中的分类常量）
    private final TypeAttribute typeAtt;
    // 记录最后一个词元的结束位置
    private int endPosition;

    public MyIKTokenizer(Reader in) {
        this(in, false);
    }

    public MyIKTokenizer(Reader in, boolean useSmart) {
        offsetAtt = addAttribute(OffsetAttribute.class);
        termAtt = addAttribute(CharTermAttribute.class);
        typeAtt = addAttribute(TypeAttribute.class);
        _IKImplement = new IKSegmenter(input, useSmart);
    }

    @Override
    public boolean incrementToken() throws IOException {
        // 清除所有的词元属性
        clearAttributes();
        Lexeme nextLexeme = _IKImplement.next();
        if (nextLexeme != null) {
            // 将Lexeme转成Attributes
            // 设置词元文本
            termAtt.append(nextLexeme.getLexemeText());
            // 设置词元长度
            termAtt.setLength(nextLexeme.getLength());
            // 设置词元位移
            offsetAtt.setOffset(nextLexeme.getBeginPosition(),
                    nextLexeme.getEndPosition());
            // 记录分词的最后位置
            endPosition = nextLexeme.getEndPosition();
            // 记录词元分类
            typeAtt.setType(nextLexeme.getLexemeTypeString());
            // 返会true告知还有下个词元
            return true;
        }
        // 返会false告知词元输出完毕
        return false;
    }

    public void reset() throws IOException {
        super.reset();
        _IKImplement.reset(input);
    }

    @Override
    public final void end() {
        // set final offset
        int finalOffset = correctOffset(this.endPosition);
        offsetAtt.setOffset(finalOffset, finalOffset);
    }

}

```

IKAnalyzer.cfg.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
    <comment>IK Analyzer 扩展配置</comment>
    <!--用户可以在这里配置自己的扩展字典 -->
    <entry key="ext_dict">local.dic;</entry>
    <!--用户可以在这里配置自己的扩展停止词字典 -->
    <entry key="ext_stopwords">stop.dic;</entry>
</properties>
```

![image-20201120150131800](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20201120150131800.png)