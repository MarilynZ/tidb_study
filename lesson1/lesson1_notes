## 第一节 tidb 总体架构

预习：
1. How we build TiDB  https://pingcap.com/blog-cn/how-do-we-build-tidb/
2. 三篇文章了解 TiDB 技术内幕 - 说存储  https://pingcap.com/blog-cn/tidb-internal-1/
3. 三篇文章了解 TiDB 技术内幕 - 说计算  https://pingcap.com/blog-cn/tidb-internal-2/
4. 三篇文章了解 TiDB 技术内幕 - 谈调度  https://pingcap.com/blog-cn/tidb-internal-3/

课件：
1. tidb 整体架构  https://www.bilibili.com/video/BV17K411T7Kd

作业：
本地下载 TIDB、TIKV、PD 源码, 改写源码并编译部署以下环境
1 tidb
1 pd
3 tikv
实现TIDB启动事物时，会打出一个 "hello transaction" 日志


实现过程：

1. 改源码 输入 "hello transaction" 日志
修改 session.go 文件
cd /tidb/session/session.go

`func (s *session) PrepareTxnCtx(ctx context.Context) {
 	if s.txn.validOrPending() {
 		return
 	}
 	logutil.BgLogger().Info("hello transaction")
 	is := domain.GetDomain(s).InfoSchema()
 `

2. 部署 TIDB 环境 
TIDB 共涉及 3 个项目, 启动顺序是PD --> TIKV --> TIDB

2.1 启动 PD 项目
git clone git@github.com:MarilynZ/pd.git

编译
cd pd && make

创建文件
mkdir tidb_data/pd

启动
nohup ./pd-server --name=pd1 --client-urls=http://127.0.0.1:2379 --peer-urls=http://127.0.0.1:2380 --data-dir=/Users/zhaomeng/Zhihu/code/tidb_data/pd  > pd.log 2>&1 &
 

2.2 启动 TIKV 项目
git clone git@github.com:MarilynZ/tikv.git

编译
cd tikv && make

创建文件
mkdir tidb_data/tikv
mkdir tidb_data/tik2
mkdir tidb_data/tik3

启动 3 个 tikv
cd target/release/
nohup ./tikv-server --addr 127.0.0.1:20160 --advertise-addr 127.0.0.1:20160 --pd 127.0.0.1:2379 --data-dir /Users/zhaomeng/Zhihu/code/tidb_data/tikv > tikv.log 2>&1 &
nohup ./tikv-server --addr 127.0.0.1:20161 --advertise-addr 127.0.0.1:20161 --pd 127.0.0.1:2379 --data-dir /Users/zhaomeng/Zhihu/code/tidb_data/tikv2 > tikv2.log 2>&1 &
nohup ./tikv-server --addr 127.0.0.1:20162 --advertise-addr 127.0.0.1:20162 --pd 127.0.0.1:2379 --data-dir /Users/zhaomeng/Zhihu/code/tidb_data/tikv3 > tikv3.log 2>&1 &

可能出现的报错和解决方案：
"the maximum number of open file descriptors is too small, got 256, expect greater or equal to 82920"

`sudo launchctl limit maxfiles 1000000 1000000`

2.3 启动 TIDB 项目
git clone git@github.com:MarilynZ/tidb.git

编译
cd tidb && make

创建日志文件
mkdir tidb_data/tidb
touch tidb_data/tidb/tidb.log

启动
nohup ./tidb-server --store=tikv --path="127.0.0.1:2379" --log-file=/Users/zhaomeng/Zhihu/code/tidb_data/tidb/tidb.log &


3. 测试

3.1 登录数据库（默认端口 4000，用户名 密码 root/Dest ）
 mysql -h127.0.0.1 -P4000 -uroot -Dtest

![tidb数据库](images/tidb.png)

3.3 查看实时日志, 可看到 "hello transaction"
tailf -f tidb_data/tidb/tidb.log

[2020/08/16 18:39:57.471 +08:00] [INFO] [session.go:2168] ["hello transaction"]
[2020/08/16 18:39:57.472 +08:00] [INFO] [session.go:2168] ["hello transaction"]
[2020/08/16 18:39:57.472 +08:00] [INFO] [session.go:2168] ["hello transaction"]

![log](images/log.png)