#!/usr/bin/env python
# -*- coding: utf-8 -*-
import os
from file_util import FileUtil
import re

class CMake_service():
    def get_cmake_dep(self):
        # 根据文件名获取指定文件列表
        files = FileUtil().get_files('C:\\Users\\Administrator\\Desktop\\CODE\\git\\go-build-template','CMakeLists.txt')
        if not len(files):
            return

        # 获取指定文件内容
        dep = []
        for file in files:
            content = FileUtil().get_file_content('C:\\Users\\Administrator\\Desktop\\CODE\\git\\go-build-template', file)
            # 匹配关键字
            keywords_list = ['find_package']
            result = []
            for keyword in keywords_list:
                result += re.findall(keyword + '.*', content, re.I)
            try:
                if len(result):
                    for ta in result:
                        tb = ta.split('(')[1]
                        if tb and tb.endswith(')'):
                            tc = tb.strip().split(')')[0].split(' ')
                            td = tc[0].lower()
                            dep.append(td)
                        elif tb and not tb.endswith(')'):
                            te = tb.strip().split(' ')[0].lower()
                            dep.append(te)
            except:
                print('cannot find deps!')
        print(FileUtil().list_filter(dep))
        return dep

if __name__ == '__main__':
    CMake_service().get_cmake_dep()

