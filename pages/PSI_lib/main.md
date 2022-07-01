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

sendProtol和recvProtol的原型是std::function类型，并在这个例子中具体指向./frontend/ecdhMain.cpp中的EcdhSend和EcdhRecv:

<details><summary>代码细节</summary>
<p>
    
```cpp
./frontend/ecdhMain.cpp
	
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
    
</p>
</details>
    
容易看出最核心的代码是sendPSIs.sendInput(set, sendChls)和recvPSIs.sendInput(set, chls)，即sender(server)向对方发送自己集合set，以及receiver向对方放自己集合set。这里的集合set是随机生成的，集合的每一个元素(集合一共有N个元素)是block类型的变量，block类型可以简单理解为两个64bit的拼起来的128bit数据，即
<details><summary>代码细节</summary>
<p>
    
```cpp
./thirdparty/libOTe/cryptoTools/cryptoTools/Common/block.h
	
namespace osuCrypto
{
    struct alignas(16) block
    {

        std::uint64_t mData[2];

        block() = default;
        block(const block&) = default;
        block(uint64_t x1, uint64_t x0)
        {

            as<uint64_t>()[0] = x0;
            as<uint64_t>()[1] = x1;
        };

        block(char e15, char e14, char e13, char e12, char e11, char e10, char e9, char e8, char e7, char e6, char e5, char e4, char e3, char e2, char e1, char e0)
        {


            as<char>()[0] = e0;
            as<char>()[1] = e1;
            as<char>()[2] = e2;
            as<char>()[3] = e3;
            as<char>()[4] = e4;
            as<char>()[5] = e5;
            as<char>()[6] = e6;
            as<char>()[7] = e7;
            as<char>()[8] = e8;
            as<char>()[9] = e9;
            as<char>()[10] = e10;
            as<char>()[11] = e11;
            as<char>()[12] = e12;
            as<char>()[13] = e13;
            as<char>()[14] = e14;
            as<char>()[15] = e15;

        }


        template<typename T>
        typename std::enable_if<
            std::is_standard_layout<T>::value&&
            std::is_trivial<T>::value &&
            (sizeof(T) <= 16) &&
            (16 % sizeof(T) == 0)
            ,
            std::array<T, 16 / sizeof(T)>&
        >::type as()
        {
            return *(std::array<T, 16 / sizeof(T)>*)this;
        }

        template<typename T>
        typename std::enable_if<
            std::is_standard_layout<T>::value&&
            std::is_trivial<T>::value &&
            (sizeof(T) <= 16) &&
            (16 % sizeof(T) == 0)
            ,
            const std::array<T, 16 / sizeof(T)>&
        >::type as() const
        {
            return *(const std::array<T, 16 / sizeof(T)>*)this;
        }

        ...

    }
}
```

 </p>
</details>

 那么问题来了，sendPSIs.sendInput(set, sendChls)和recvPSIs.sendInput(set, chls)的内部逻辑又是什么呢？可以在./libPSI/PSI/ECDH/EcdhPsiSender.cpp和EcdhPsiReceiver.cpp查到:

<details><summary>代码细节</summary>
<p>
    
```cpp
./libPSI/PSI/ECDH/EcdhPsiSender.cpp
	
void EcdhPsiSender::sendInput(std::vector<block>& inputs, span<Channel> chls)
{


    //u64 theirInputSize = inputs.size();

	u64 maskSizeByte = u64(40 + 2*log2(inputs.size())+7) / 8;

    //std::vector<PRNG> thrdPrng(chls.size());
    //for (u64 i = 0; i < thrdPrng.size(); i++)
    //    thrdPrng[i].SetSeed(mPrng.get<block>());

    auto RsSeed = mPrng.get<block>();

	std::vector<std::vector<u8>> sendBuff2(chls.size());

    auto routine = [&](u64 t)
    {
        u64 inputStartIdx = inputs.size() * t / chls.size();
        u64 inputEndIdx = inputs.size() * (t + 1) / chls.size();
        u64 subsetInputSize = inputEndIdx - inputStartIdx;


        auto& chl = chls[t];
        //auto& prng = thrdPrng[t];

        using Curve = REllipticCurve;
        using Point = REccPoint;
        //using Brick = REccPoint;
        using Number = REccNumber;
        Curve curve;


      
        RandomOracle inputHasher(sizeof(block));
		Number a(curve);
		Point xa(curve), point(curve), yb(curve), yba(curve);
        a.randomize(RsSeed);

		std::vector<u8> sendBuff(xa.sizeBytes() * subsetInputSize);
		auto sendIter = sendBuff.data();
		sendBuff2[t].resize(maskSizeByte * subsetInputSize);
		auto sendIter2 = sendBuff2[t].data();

		std::vector<u8> recvBuff(yb.sizeBytes() * subsetInputSize);
        std::vector<u8> temp(yba.sizeBytes());

		//send H(x)^a
        for (u64 i = inputStartIdx ; i < inputEndIdx; ++i)
        {
            block seed;
            inputHasher.Reset();
            inputHasher.Update(inputs[i]);
            inputHasher.Final(seed);

			point.randomize(seed);
            //std::cout << "sp  " << point << "  " << toBlock(hashOut) << std::endl;

			xa = (point * a);

			xa.toBytes(sendIter);
			sendIter += xa.sizeBytes();
        }
		chl.asyncSend(std::move(sendBuff));


		//recv H(y)^b
		chl.recv(recvBuff);
		auto recvIter = recvBuff.data();

		//send H(y)^b^a
        for (u64 i = inputStartIdx; i < inputEndIdx;i++)
        {
			yb.fromBytes(recvIter); recvIter += yb.sizeBytes();
			yba = yb*a;

            
            yba.toBytes(temp.data());
            RandomOracle ro(sizeof(block));
            ro.Update(temp.data(), temp.size());
            block blk;
            ro.Final(blk);
            memcpy(sendIter2, &blk, maskSizeByte);

			sendIter2 += maskSizeByte;
        }
		//std::cout << "dones send H(y)^b^a" << std::endl;

    };


    std::vector<std::thread> thrds(chls.size());
    for (u64 i = 0; i < u64(chls.size()); ++i)
    {
        thrds[i] = std::thread([=] {
            routine(i);
        });
    }


    for (auto& thrd : thrds)
        thrd.join();

	for (u64 i = 0; i < u64(chls.size()); ++i)
	{
		thrds[i] = std::thread([=] {
			auto& chl = chls[i];
			chl.asyncSend(std::move(sendBuff2[i]));
		});
	}


	for (auto& thrd : thrds)
		thrd.join();

	//std::cout << "S done" << std::endl;

}
``` 
    
</p>
</details>
对EcdhPsiSender.cpp的一些解读。第一步，sender做运算 $H(x)^a$ 并借由Channel发送给receiver: 这里x是sender集合中的任意一个元素，a是一个随机数，H(x)是通过Random Oracle inputHasher实现的，最终输出point=H(x)和 xa=H(x)^a; 第二步，sender借由channel收到来自receiver的 $H(y)^b$ 并计算 $H(y)^{ba}$：注意代码中yba=H(y)^ba,yba最终通过Random Oracle ro哈希成128bit的blk并通过chl.asyncSend(）发送给receiver。

	
<details><summary>代码细节</summary>
<p>
    
```cpp	
./libPSI/PSI/ECDH/EcdhPsiSender.cpp
	
void EcdhPsiReceiver::sendInput(
    span<block> inputs,
    span<Channel> chls)
{
    //std::vector<PRNG> thrdPrng(chls.size());
    //for (u64 i = 0; i < thrdPrng.size(); i++)
    //    thrdPrng[i].SetSeed(mPrng.get<block>());


	std::vector<block> thrdPrngBlock(chls.size());
	std::vector<std::vector<u64>> localIntersections(chls.size() - 1);

	u64 maskSizeByte = u64(40 + 2*log2(inputs.size()) + 7) / 8;

    auto RcSeed = mPrng.get<block>();

	std::unordered_map<u32, block> mapXab;
	mapXab.reserve(inputs.size());


	auto routine = [&](u64 t)
	{
		u64 inputStartIdx = inputs.size() * t / chls.size();
		u64 inputEndIdx = inputs.size() * (t + 1) / chls.size();
		u64 subsetInputSize = inputEndIdx - inputStartIdx;


		auto& chl = chls[t];
		//auto& prng = thrdPrng[t];
		u8 hashOut[RandomOracle::HashSize];
        RandomOracle inputHasher;

		std::vector<u8> sendBuff(yb.sizeBytes() * subsetInputSize);
		auto sendIter = sendBuff.data();

		std::vector<u8> recvBuff(xa.sizeBytes() * subsetInputSize);
		std::vector<u8> recvBuff2(xab.sizeBytes() * subsetInputSize);

	//	std::cout << "send H(y)^b" << std::endl;

		//send H(y)^b
		for (u64 i = inputStartIdx; i < inputEndIdx; ++i)
		{

			inputHasher.Reset();
			inputHasher.Update(inputs[i]);
			inputHasher.Final(hashOut);

			point.randomize(toBlock(hashOut));
			//std::cout << "sp  " << point << "  " << toBlock(hashOut) << std::endl;

			yb = (point * b);

			yb.toBytes(sendIter);
			sendIter += yb.sizeBytes();
		}
		chl.asyncSend(std::move(sendBuff));


		//recv H(x)^a
		//std::cout << "recv H(x)^a" << std::endl;

		chl.recv(recvBuff);
		auto recvIter = recvBuff.data();

		//compute H(x)^a^b as map
		//std::cout << "compute H(x)^a^b " << std::endl;

		for (u64 i = inputStartIdx; i < inputEndIdx;i++)
		{
			xa.fromBytes(recvIter); recvIter += xa.sizeBytes();
			xab = xa*b;
			
			xab.toBytes(temp.data());

            RandomOracle ro(sizeof(block));
            ro.Update(temp.data(), temp.size());
            block blk;
            ro.Final(blk);
			auto idx = blk.as<u32>()[0];

            mapXab.insert({ idx, blk });

		}
	};


    std::vector<std::thread> thrds(chls.size());
    for (u64 i = 0; i < u64(chls.size()); ++i)
    {
        thrds[i] = std::thread([=] {
            routine(i);
        });
    }


	for (auto& thrd : thrds)
		thrd.join();

	auto routine2 = [&](u64 t)
	{
		u64 inputStartIdx = inputs.size() * t / chls.size();
		u64 inputEndIdx = inputs.size() * (t + 1) / chls.size();
		u64 subsetInputSize = inputEndIdx - inputStartIdx;


		auto& chl = chls[t];


		std::vector<u8> recvBuff2(maskSizeByte * subsetInputSize);

		//recv H(y)^b^a
		chl.recv(recvBuff2);
		auto recvIter2 = recvBuff2.data();

		for (u64 i = inputStartIdx; i < inputEndIdx; i++)
		{

			auto& idx_yba = *(u32*)(recvIter2);

			auto id = mapXab.find(idx_yba);
			if (id != mapXab.end()) {

				//std::cout << "id->first[" << i << "] " << toBlock(id->first) << std::endl;

				if (memcmp(recvIter2, &id->second, maskSizeByte) == 0)
				{
					//std::cout << "intersection item----------" << i << std::endl;
					if (t == 0)
						mIntersection.emplace_back(i);
					else
						localIntersections[t - 1].emplace_back(i);
				}
			}
			recvIter2 += maskSizeByte;

		}
		//std::cout << "done" << std::endl;

	};


	for (u64 i = 0; i < u64(chls.size()); ++i)
	{
		thrds[i] = std::thread([=] {
			routine2(i);
		});
	}

	for (auto& thrd : thrds)
		thrd.join();

	u64 extraSize = 0;

	for (u64 i = 0; i < thrds.size()-1; ++i)
		extraSize += localIntersections[i].size();

	mIntersection.reserve(mIntersection.size() + extraSize);
	for (u64 i = 0; i < thrds.size()-1; ++i)
	{
		mIntersection.insert(mIntersection.end(), localIntersections[i].begin(), localIntersections[i].end());
	}


}
	
``` 
    
</p>
</details>	

对ecdhPsiReceiver.cpp的一些解读。Receiver的逻辑比Sender要复杂些，额外增加的工作量主要是求交集的运算。
第一步，在线程routine内进行三个子操作：发送 $H(y)^b$ ， 接收 $H(x)^a$ 并计算 $H(x)^{ab}$, $H(x)^{ab}$ 最终以哈希表unordered_map mapXab的形式存储,核心代码为
	
```cpp 
// convert EC point xab to byte-array temp
xab.toBytes(temp.data());
RandomOracle ro(sizeof(block));
ro.Update(temp.data(), temp.size());
block blk;
// covert the byte-array temp to block blk
ro.Final(blk);
// set the first 32 bits of blk as key
auto idx = blk.as<u32>()[0];
// set the entire blk as the value
mapXab.insert({ idx, blk });
``` 

第二步，在线程routine2内进行两个自操作，即接收来自sender的$H(y)^{ba}$ (以block的格式存储)并和线程routine内计算好的 $H(x)^{ab}$ (以哈希表mapXab格式存储)求交集。求交的方法是以$H(y)^{ba}$ 的首32bit数据为key，在mapXab查找是否有对应的value存在。若value值存在，说明该$H(y)^{ba}$处于交集内（注意代码最终记录的是处于交集的$H(y)^{ab}$的位置索引i）。例子程序里设置的是sender 和 receiver的集合是**一模一样的**（集合元素通过PRNG驱动生成，设置PRNG seed一样就可以保证输出一致）。

	
	
