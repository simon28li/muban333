import logging
import logging.handlers
import threading
import queue

log_mgr = None
flag = "success"
download_queue = queue.Queue()  # soft_name 集合 下载
getdep_queue = queue.Queue()  # soft_name 集合 提取
dep_analyse_queue = queue.Queue()  # soft_name 集合 分析
#获取软件列表
def get_softlist():
    soft_list = []
    for n in range(3000):
        soft_list.append(n)
    return soft_list

# 下载函数
def soft_download(soft_name):
    print(soft_name + "下载")
    return soft_name, flag

# 软件提取
def get_softdep(soft_name, flag="success"):
    dep_list = [{
        'dep_name':'B',
        'dep_version':'1.1'
    }]
    dep_dict = {
        'soft_name' :soft_name,
        'soft_deps' :dep_list,
        'flag':flag
    }
    return dep_dict

# 软件分析
def dep_analyse(dep_dict):
    soft_list = []
    return soft_list

# 生成package
def gen_package(soft_list):
    return

class LogMgr:
    def __init__(self, logpath):
        self.LOG = logging.getLogger('log')
        loghd = logging.handlers.RotatingFileHandler(logpath, "a", 0, 1)
        fmt = logging.Formatter("%(asctime)s %(threadName)-10s %(message)s", "%Y-%m-%d %H:%M:%S")
        loghd.setFormatter(fmt)
        self.LOG.addHandler(loghd)
        self.LOG.setLevel(logging.INFO)

    def info(self, msg):
        if self.LOG is not None:
            self.LOG.info(msg)


class Worker(threading.Thread):
    global log_mgr

    def __init__(self, name):
        threading.Thread.__init__(self)
        self.name = name

    def run(self):
        while True:
            try:
                task = download_queue.get(False)
                if task:
                    log_mgr.info("HANDLE_TASK: %s" % task)
                    getdep_queue.put(task)
            except queue.Empty:
                break
        return


def main():
    global log_mgr
    log_mgr = LogMgr("mylog")
    # 获取软件列表
    soft_list = get_softlist()
    # 获取列表长度
    soft_list_len = len(soft_list)
    if soft_list:
        for i in soft_list:
            download_queue.put("softname_"+str(i))
    #for i in range(30):
        #download_queue.put("data"+str(i))

    workers = []
    for i in range(3):
        w = Worker("worker"+str(i))
        workers.append(w)

    for i in range(3):
        workers[i].start()

    for i in range(3):
        workers[i].join()

    total_num = download_queue.qsize()
    log_mgr.info("TOTAL_HANDLE_TASK: %d" % total_num)
    exit(0)


if __name__ == '__main__':
    main()
