#!/usr/bin/env python
# -*- coding: utf-8 -*-
import os
from file_util import FileUtil
import re

class CAutotools_service():
    def get_autotools_dep(self):
        # 根据文件名获取指定文件列表
        files = FileUtil().get_files('C:\\Users\\Administrator\\Desktop\\CODE\\git\\go-build-template','configure.ac')
        if not len(files):
            return

        # 获取指定文件内容
        dep = []
        for file in files:
            content = FileUtil().get_file_content('C:\\Users\\Administrator\\Desktop\\CODE\\git\\go-build-template', file)
            # 匹配关键字
            keywords_list = ['AC_CHECK_LIB']
            result = []
            for keyword in keywords_list:
                result += re.findall(keyword + '.*', content, re.I)
            try:
                if len(result):
                    for ta in result:
                        tb = ta.split('(')[1]
                        if tb and  ',' in tb:
                            tc = tb.strip().split(',')[0]
                            td = re.findall(r'[[](.*?)[]]', tc)
                            te = td[0].lower()
                            dep.append(te)
                        elif tb and not tb.endswith(')'):
                            te = tb.strip().split(' ')[0].lower()
                            dep.append(te)
            except:
                print('cannot find deps!')
        print(FileUtil().list_filter(dep))
        return dep

if __name__ == '__main__':
    CAutotools_service().get_autotools_dep()

