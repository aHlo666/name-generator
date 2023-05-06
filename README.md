# 起名生成器

基本思路

任何中文名, 都由姓+两个中文字符构成. 其中名的发音必为所有可能中文发音的组合

所以, 可以通过遍历的方式, 生成所有可能的中文名称发音. 然后先选发音, 再替换文字.

使用规则:

0.  起名用字
    1.  从 3500 常见字中选字
    2.  从知名学校录取名单中选字, 尽量避免使用姓氏作为姓名选字
1.  声调
    1.  声调分为: 一声平（阴平）、二声扬（阳平）、三声拐弯（上）、四声降（去）四种, 轻声视为一声
    2.  禁止同声调
        1.  阴阴阴、阳阳阳、上上上、去去去，没有变化很难有美感
    3.  排除一些两字连缀效果不佳的组合
        1.  `阴阴X` / `X上上` / `X去去` / `上上X`
    4.  排除上声（三声）结尾
    5.  性别相关
        1.  男宝: 第二声（阳平）或第四声（去声），名字会比较响亮，干脆利落
        2.  女宝: 尾字为第一声（阴平）比较温和
2.  不使用叠字/叠音(相对简单, 不需要生成器参与)
3.  避免同名
    1.  不能与直系亲属的名字同字/同音
    2.  直系亲属指：父母、祖父母（孩子父亲的父母）、外祖父母（孩子母亲的父母）、伯叔姑（父亲的兄弟姐妹）、舅姨（母亲的兄弟姐妹）

# 扩展资料

姓名库来源:

- 思路 1: 知名院校硕士/博士录取名单, 关键词: `硕士研究生招生` + `拟录取名单`/`招生录取名单` + `xls`
- 思路 2: 私募基金名单, 基金名称一般都经过筛选, 关键词: 私募排排网
- 思路 3: 成绩公示 , 关键词: `成绩公示` + `xls`

为避免侵犯隐私, 不提交姓名库原始数据, 只在 json 中记录解析后的人名可选词结果. 姓名库原始数据要求: 每行一个人名

解析规则为: 2 字名记录最后一字, 3/4/5 字名记录最后 2 字, 其他情况不考虑, 排除姓氏之后的结果作为人名库数据

# 备注

```php
对161733个人名用字的统计结果显示, 共出现了3294个人名用字, 其中

1%的字被使用了1345次以上, 这种字共32个
5%的字被使用了512次以上, 这种字共164个
10%的字被使用了201次以上, 这种字共329个
20%的字被使用了69次以上, 这种字共658个
30%的字被使用了23次以上, 这种字共988个
40%的字被使用了11次以上, 这种字共1317个

被使用数大于10次的字共1330个, 占比40%
被使用数大于5次的字共1640个, 占比49%
被使用数大于4次的字共1757个, 占比53%
被使用数大于3次的字共1922个, 占比58%
被使用数大于2次的字共2097个, 占比63%
被使用数大于1次的字共2433个, 占比73%
```

# 实现思路

1.  生成汉字-拼音数据库, 作为原始值
2.  对汉字-拼音数据库进行筛选
    1.  移除多音字
    2.  按等级移除
        1.  所有未出现在姓名库中的汉字
        2.  所有在姓名库中出现频率小于 10/5/3/2/1 的汉字(方便增加候选字)
3.  生成姓名时, 若姓氏为多音字, 用户需主动选择对应拼音(以便根据声调进行筛选)
4.  支持排除指定同音字, 避免同名
5.  在排除已有音律规则的情况下, 可得到所有可选姓名发音, 支持导出为 excel
