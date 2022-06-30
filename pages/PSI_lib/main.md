# libPSI 阅读笔记

这里记录Private Set Intersection(PSI)研究方向的一个重要开源库libPSI。 libPSI的作者Peter Rindal在读博期间完成它的开发，该库汇总了几乎所有重要的PSI的各式方法和实现，具有很高的学习价值。

## libPSI的编译和安装

这里以我的配置环境为例(VMware虚拟机下的Ubuntu 20.04 LTS)，在GitHub下载并编译[libPSI](https://github.com/osu-crypto/libPSI)。

```
git clone https://github.com/osu-crypto/libPSI.git
cd libPSI
python build.py
```

更详尽的编译指导可以参考libPSI的README.md。这里最关键的是build.py，它实现了cmake配置和编译的命令，具体有下面三条命令组成：

```
mkdir -p out/build/linux
cmake   -S . -B out/build/linux  -DSUDO_FETCH=OFF -DENABLE_ALL_PSI=ON -DFETCH_AUTO=ON -DPARALLEL_FETCH=8 -DCMAKE_BUILD_TYPE=Release 
cmake --build out/build/linux   --parallel 8 
```


## 运行libPSI的frontend.exe

现在运行libPSI编译生成的前端frontend.exe：

```
./out/build/linux/frontend/frontend.exe
```
此时frontend.exe会打印自己支持的PSI协议(包括dcwr,ff16,rr17a,rr17a-sm,rr17b,grr,dkt,ecdh,kkrt) ，文件相关参数，和benchmark参数。

这里以最经典的ecdh协议(ecdh-based PSI原理可以参考[这篇博文](https://github.com/davidhoo1988/davidhu.github.io/blob/gh-pages/pages/std_PSI/main.md))为例。

首先开启PSI_lib的PRINT宏定义，这样./libPSI/PSI/ECDH/EcdhPSISender.cpp和EcdhPSIReceiver.cpp的和额外信息打印相关的代码被激活。具体地，在./libPSI/config.h.in添加下面一行代码：

```
// build the library with PRINT enabled
#cmakedefine PRINT  @PRINT@
```

接着使用build.py重新配置和编译cmake：

```
python3 build.py --debug -DLIBPSI_ENABLE_X=ON -DPRINT=ON
```

```
./out/build/linux/frontend/frontend.exe -ecdh
```

现在重点讨论上面一行ECDH-based PSI的代码逻辑。事实上，frontend.exe -ecdh启动的是./frontend/main.cpp第450行代码

```cpp
benchmark(ecdhTags, cmd, EcdhRecv, EcdhSend)
```

benchmark函数的主逻辑(./frontend/main.cpp第271-281行)是Server执行数据发送协议sendProtol(params)，接着Client执行数据接收协议recvProtol(params)。下面重点介绍这两个数据协议:

sendProtol和recvProtol的原型是std::function类型，并在这个例子中具体指向./frontend/ecdhMain.cpp中的

```cpp
void EcdhSend(LaunchParams& params)
{
    PRNG prng(_mm_set_epi32(4253465, 3434565, 234435, 23987045));

    for (auto setSize : params.mNumItems)
    {
        for (auto numThreads : params.mNumThreads)
        {
            auto sendChls = params.getChannels(numThreads);
            std::vector<block> set(setSize);
            prng.get(set.data(), set.size());
            EcdhPsiSender sendPSIs;

            ...
            
            sendPSIs.sendInput(set, sendChls);
        }
    }
}

void EcdhRecv(LaunchParams& params)
{
    PRNG prng(_mm_set_epi32(4253465, 3434565, 234435, 23987045));
    for (auto setSize : params.mNumItems)
    {
        for (auto numThreads : params.mNumThreads)
        {
            auto chls = params.getChannels(numThreads);
            std::vector<block> set(setSize);
            prng.get(set.data(), set.size());

            EcdhPsiReceiver recvPSIs;

            ...

            recvPSIs.sendInput(set, chls);
        }
    }
}
```
