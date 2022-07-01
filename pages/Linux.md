- #CS
- ## Network
	- ### 优化建议
		- 减少不必要的网络I/O
		- 合并网络请求 (redis->multiGet)
			- ```python3
			  if __name__ == "__main__":
			      for pkg in pkgList:
			          # multiple request is huge & slow & resource-bound 
			          redis.get(pkg)
			  ```
		- 调用者与被调用者的机器尽可能部署得近一些
		- 内网调用不要使用外网域名
			- 外网接口慢
			- 带宽成本高
			- NAT单点瓶颈
		- 调整网卡RingBuffer大小
			- > 在Linux的整个网络栈中，RingBuffer充当了一个任务收发中转站的角色。对于接收过程，网卡负责往RingBuffer写入收到的数据帧，`ksoftirqd` 内核线程负责从中取走处理。**如果RingBuffer被填满了，网卡会直接丢弃后续的数据包，不做任何处理。**
		- 减少内存拷贝 `mmap`, `sendfile`
		- 使用 eBPF 绕开协议栈的本机 I/O
		- 尽量少用 `recvfrom` 等进程阻塞的方式
		- 使用成熟的网络库
		- 使用 `Kernel-ByPass` 新技术
		- 配置充足的端口范围
		- 小心连接队列溢出
		- 减少握手重试 `tcp_synack_retries`
		- 如果请求频繁，启用短连接改用长连接
			- 节约握手开销
			- 规避队列满的问题
			- 端口数不一定出问题
		- `TIME_WAIT` 的优化
			- 开启端口 `reuse` 和 `recycle`
			- ```sh
			  # 限制 TIME_WAIT 状态连接的最大数量
			  # vi /etc/sysctl.conf
			  net.ipv4.tcp_max_tw_buckets = 32768
			  # sysctl -p
			  ```
- ## Reference
	- [Linux 网络性能的 15 个优化建议！](https://mp.weixin.qq.com/s/GNG263EaQcUVmBff5Mzxnw)