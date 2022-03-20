#!/usr/bin/env python
# -*- coding: utf-8 -*-
import os
from file_util import FileUtil
import re

class Makefile_service():
    def get_makefile_dep(self):
        # 根据文件名获取指定文件列表
        files = FileUtil().get_files('C:\\Users\\Administrator\\Desktop\\CODE\\git\\go-build-template','CMakeLists.txt')
        if not len(files):
            return
        # 获取指定文件内容
        dep = []
        for file in files:
            content = FileUtil().get_file_content('C:\\Users\\Administrator\\Desktop\\CODE\\git\\go-build-template', file)
            # 匹配关键字
            keywords_list = ['LDFLAGS', 'LIBFLAGS']
            result = []
            for keyword in keywords_list:
                result += re.findall(keyword + '.*', content, re.I)
            try:
                if len(result):
                    for a in result:
                        b = a.split('=')[1]
                        if b:
                            c = b.strip().split(' ')
                            for d in c:
                                if d.startswith('-l'):
                                    e = d[2:]
                                    dep.append('lib' + e)
            except:
                print('cannot find deps!')
        print(FileUtil().list_filter(dep))

if __name__ == '__main__':
    Makefile_service().get_makefile_dep()

