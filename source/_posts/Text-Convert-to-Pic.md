---
title: Text Convert to Pic
top: false
cover: false
toc: true
mathjax: false
date: 2019-10-08 12:53:59
password:
summary: 文字转图片
tags:
- CV
- Scripts
categories:
- Scripts
---
```python
from PIL import Image , ImageDraw, ImageFont
import os

def ttp(text, fone_type_file, font_size, font_color, position, row_lens):
    """
    :param text: 要插入的文字
    :param fone_type_file: 文字类型文件名称
    :param font_size: 文字大小
    :param position: 要插入的位置
    :param row_lens: 一行的字数
    :return:
    """
    im = Image.open('bg1.png')
    datas = text.split('\n')
    data = ''
    if not datas:
        datas = [text]
    for d in datas:
        tempd = ''
        if not d:
            tempd = ' '
        lend = len(d)
        for i in range(0, lend, row_lens):
            tempd = tempd + d[i:i+row_lens] + '\n\n'

        # elif len(d) > 60:
        #     d1 = d[:60] + '\n'
        #     d2 = d[60:]
        #     d = d1 + ' \n'+ d2
        data += (tempd +'\n')
        #data += ' \n'

    data = data[:-1]

    dr = ImageDraw.Draw(im)
    font = ImageFont.truetype(fone_type_file, font_size)
    dr.text(position, data, font=font, fill= font_color, spacing=0, align='left')
    return im

if __name__=="__main__":

    #str = "fafafffffffff测试fafafafafafafafafafafafaf"
    #target_file = target_file.resize((1000, 600))
    # target_file.save("bg1.png",'PNG')
    str = ''
    row_pos = 1
    for line2 in open("question.txt"):
        str = str + line2
    # 字体文件需要先下好
    target_file = ttp(str, './FangZhengHeiTiJianTi-1.ttf',20,'#000000', (20, 10), 50)

    target_file.show()
    target_file.save("res.png",'PNG')
```
