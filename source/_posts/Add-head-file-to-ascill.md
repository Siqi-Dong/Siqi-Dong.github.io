---
title: Add head file to ascill
date: 2020-10-20 15:15:58
author: Siqi Dong
categories: TextFile
---

```python
# -*- coding: utf-8 -*-
import os

def addAsciiHeadFile(asciiFolder):
    rootdir = os.path.join(asciiFolder)
    for (dirpath, dirnames, filenames) in os.walk(rootdir):
        for filename in filenames:
            if os.path.splitext(filename)[1] == '.txt':
                asciiPath=asciiFolder+"\\"+filename
                with open(asciiPath, 'r+') as f:
                    content = f.read()
                    f.seek(0, 0)
                    # head file content
                    f.write('XXX\n' + 'XXX\n' + 'XXX\n' + 'XXX\n' + 'XXX\n' + 'XXX\n' + content)
                    print asciiPath + "done!"
```