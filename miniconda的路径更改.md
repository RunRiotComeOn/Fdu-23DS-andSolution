# Miniconda 的路径更改

### 写在前面
这个教程主要是针对 Linux 系统，但是我猜测 Windows 也是大同小异。当你已经安装好，甚至使用过一段时间的 conda （这里指已经创建过环境），却因为存储空间等原因需要将 Miniconda 的安装路径更改时，这个教程会非常适用。我是在租用的 GPU 服务器上遇到的这个问题。当时 Miniconda 下载在系统盘中，但是系统盘空间只有 30 个 G ，多创了几个环境就崩掉了，无法下载更多的包，所以只好把 Miniconda 移动到数据盘中，才能继续当时的项目。相信这种情况在各位的身上可能发生的概率也不小哈哈哈。

## 备份当前环境
**备份很重要**！特别是在你已有的环境特别复杂，包下载了一个又一个，一遍又一遍，终于能跑了的情况下...

- 检查当前环境，记录所有环境名称（我这里两个环境的例子将是 mytensorf 和 mynerf）：
  
    ```bash
    conda info --envs
    ```


- 导出环境配置（可选，但强烈推荐），为每个环境生成 environment.yml 文件，将这些文件备份到安全位置（如外部存储）：
    ```bash
    conda env export -n mytensorf > mytensorf_env.yml
    conda env export -n mynerf > mynerf_env.yml
    ```

- 备份 Miniconda 目录：

    ```bash
    cp -r ~/miniconda3 ~/miniconda3_backup
    ```

## 停止使用当前 Miniconda
虽然我没试过如果不停止会有什么后果，但是学了计算机原理的同学应该都知道，如果两个进程交叉使用同一个文件，肯定会出现各种问题... 所以不要轻易尝试，还是求稳最好。

- 退出所有 Conda 环境：
    ```bash
    conda deactivate
    ```

- 检查是否仍有进程使用，确保没有 Python 或 Conda 相关进程运行，若有，**终止进程**（如 `kill -9 <PID>`）：
    ```bash
    ps aux | grep conda
    ps aux | grep python
    ```

## 转移 Miniconda
注意，你在干这事儿的时候需要确认转移的路径有足够空间和写权限。我这里假设转移前的路径是 `/root/miniconda3` ，转移后希望是 `/root/tmp/miniconda3` 。

- 移动 Miniconda 目录，使用 mv 命令：
  - 有管理员权限时：
      ```bash
      sudo mv /root/miniconda3 /root/tmp/miniconda3
      ```

  - 无权限时：
      ```bash
      mv /root/miniconda3 /root/tmp/new_miniconda3
      ```
    
- 更新路径环境变量：
  - 编辑 `~/.bashrc` ：
    ```bash
    nano ~/.bashrc
    ```

  - 查找并修改 Miniconda 初始化部分：
    ```bash
    export PATH="$/root/miniconda3/bin:$PATH"
    ```

  - 改为新路径：
    ```bash
    export PATH="/root/tmp/miniconda3/bin:$PATH"
    ```

  - 保存并应用：
    ```bash
    source ~/.bashrc
    ```

## 识别不了？不存在此路径？怎么解决
完成以上操作之后，不一定能完全解决问题。例如，你在运行某个环境下运行 pip 命令的时候，可能出现这种情况：
```bash
> conda activate mytensorf
(mytensorf) > pip install numpy

ERROR: Could not find a version that satisfies the requirement tensorflow (from versions: none)
ERROR: No matching distribution found for tensorflow
```

这时候，请检查 `/root/tmp/miniconda3/envs/cv/bin/pip`：

```python
#!/root/miniconda3/envs/mytensorf/bin/python3.9
# -*- coding: utf-8 -*-
import re
import sys

from pip._internal.cli.main import main

if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw?|\.exe)?$', '', sys.argv[0])
    sys.exit(main())
```

你会发现 `#!/root/miniconda3/envs/mytensorf/bin/python3.9` 中 bin 的路径并没有改变，改成 `#!/root/tmp/miniconda3/envs/mytensorf/bin/python3.9` 。

类似的情况也应该这样修改。完成修改之后，就可以正常使用啦！
