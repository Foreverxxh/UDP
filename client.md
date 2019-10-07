```
package main

import (
	"fmt"
	"io/ioutil"
	"net"
	"os"
	"strconv"
	"time"
)

/*var MbTable = []uint16{
	0X0000, 0XC0C1, 0XC181, 0X0140, 0XC301, 0X03C0, 0X0280, 0XC241,
	0XC601, 0X06C0, 0X0780, 0XC741, 0X0500, 0XC5C1, 0XC481, 0X0440,
	0XCC01, 0X0CC0, 0X0D80, 0XCD41, 0X0F00, 0XCFC1, 0XCE81, 0X0E40,
	0X0A00, 0XCAC1, 0XCB81, 0X0B40, 0XC901, 0X09C0, 0X0880, 0XC841,
	0XD801, 0X18C0, 0X1980, 0XD941, 0X1B00, 0XDBC1, 0XDA81, 0X1A40,
	0X1E00, 0XDEC1, 0XDF81, 0X1F40, 0XDD01, 0X1DC0, 0X1C80, 0XDC41,
	0X1400, 0XD4C1, 0XD581, 0X1540, 0XD701, 0X17C0, 0X1680, 0XD641,
	0XD201, 0X12C0, 0X1380, 0XD341, 0X1100, 0XD1C1, 0XD081, 0X1040,
	0XF001, 0X30C0, 0X3180, 0XF141, 0X3300, 0XF3C1, 0XF281, 0X3240,
	0X3600, 0XF6C1, 0XF781, 0X3740, 0XF501, 0X35C0, 0X3480, 0XF441,
	0X3C00, 0XFCC1, 0XFD81, 0X3D40, 0XFF01, 0X3FC0, 0X3E80, 0XFE41,
	0XFA01, 0X3AC0, 0X3B80, 0XFB41, 0X3900, 0XF9C1, 0XF881, 0X3840,
	0X2800, 0XE8C1, 0XE981, 0X2940, 0XEB01, 0X2BC0, 0X2A80, 0XEA41,
	0XEE01, 0X2EC0, 0X2F80, 0XEF41, 0X2D00, 0XEDC1, 0XEC81, 0X2C40,
	0XE401, 0X24C0, 0X2580, 0XE541, 0X2700, 0XE7C1, 0XE681, 0X2640,
	0X2200, 0XE2C1, 0XE381, 0X2340, 0XE101, 0X21C0, 0X2080, 0XE041,
	0XA001, 0X60C0, 0X6180, 0XA141, 0X6300, 0XA3C1, 0XA281, 0X6240,
	0X6600, 0XA6C1, 0XA781, 0X6740, 0XA501, 0X65C0, 0X6480, 0XA441,
	0X6C00, 0XACC1, 0XAD81, 0X6D40, 0XAF01, 0X6FC0, 0X6E80, 0XAE41,
	0XAA01, 0X6AC0, 0X6B80, 0XAB41, 0X6900, 0XA9C1, 0XA881, 0X6840,
	0X7800, 0XB8C1, 0XB981, 0X7940, 0XBB01, 0X7BC0, 0X7A80, 0XBA41,
	0XBE01, 0X7EC0, 0X7F80, 0XBF41, 0X7D00, 0XBDC1, 0XBC81, 0X7C40,
	0XB401, 0X74C0, 0X7580, 0XB541, 0X7700, 0XB7C1, 0XB681, 0X7640,
	0X7200, 0XB2C1, 0XB381, 0X7340, 0XB101, 0X71C0, 0X7080, 0XB041,
	0X5000, 0X90C1, 0X9181, 0X5140, 0X9301, 0X53C0, 0X5280, 0X9241,
	0X9601, 0X56C0, 0X5780, 0X9741, 0X5500, 0X95C1, 0X9481, 0X5440,
	0X9C01, 0X5CC0, 0X5D80, 0X9D41, 0X5F00, 0X9FC1, 0X9E81, 0X5E40,
	0X5A00, 0X9AC1, 0X9B81, 0X5B40, 0X9901, 0X59C0, 0X5880, 0X9841,
	0X8801, 0X48C0, 0X4980, 0X8941, 0X4B00, 0X8BC1, 0X8A81, 0X4A40,
	0X4E00, 0X8EC1, 0X8F81, 0X4F40, 0X8D01, 0X4DC0, 0X4C80, 0X8C41,
	0X4400, 0X84C1, 0X8581, 0X4540, 0X8701, 0X47C0, 0X4680, 0X8641,
	0X8201, 0X42C0, 0X4380, 0X8341, 0X4100, 0X81C1, 0X8081, 0X4040}//表
func CheckSum2(data []byte) uint16 {
	var CRC16 uint16  //定义CRC16
	CRC16 = 0xFFFF //初始化CRC16
	for _, value := range data {
		n := uint8(uint16(value)^CRC16)
		CRC16 >>= 8
		CRC16 ^= MbTable[n]
	}
	return CRC16
}*///CRC16算法（查表法）
/*func CheckSum1(data []byte) uint16 {
	i := 0;
	var CRC16 uint16
	CRC16 = 0xFFFF;
	for _, value := range data {
		CRC16 ^= uint16(value)
		for i = 0 ; i < 8 ; i++ {
			if CRC16 & 0x1 != 0 {
				CRC16 >>= 1
				CRC16 ^= 0xA001
			} else {
				CRC16 >>= 1
			}
		}
	}
	return CRC16
}*///CRC16算法（计算法）
const (
	SERVER_IP       =  "10.14.124.84"  //定义服务器IP
	SERVER_PORT     =  10006  //定义服务器端口
	timesum = time.Second //定义计时器时间间隔
)//定义服务器的IP,端口，数据报长度，窗口长度
func CheckSum(data []byte) uint16 {
	var (
		sum    uint32
		length int = len(data)
		index  int
	)
	//以每16位为单位进行求和，直到所有的字节全部求完或者只剩下一个8位字节（如果剩余一个8位字节说明字节数为奇数个）
	for length > 1 {
		/*if uint32(data[index+1])==0&&uint32(data[index+2])==0{
			sum += uint32(data[index])
			index += 1
			break
		}*/
		sum += uint32(data[index])<<8 + uint32(data[index+1])
		index += 2
		length -= 2
	}
	//如果字节数为奇数个，要加上最后剩下的那个8位字节
	if length > 0 {
		sum += uint32(data[index])
	}
	//加上高16位进位的部分
	sum += (sum >> 16)
	//返回的时候求反
	return uint16(^sum)
}//校验和算法
func Uint16ToBytes(n uint16) []byte {
	return []byte{
		byte(n),
		byte(n >> 8),
	}
}//uint16  ->  byte
func checkError(err error) {
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
}//检错
func makedata( toWrite string,cnt int,) string {
	toWritelen := uint16(len(toWrite))//计算长度
	abg := CheckSum([]byte(toWrite))//计算校验和
	toWrite = string(cnt) + string(Uint16ToBytes(abg)) + string(Uint16ToBytes(toWritelen)) + toWrite//将序号与校验和与数据长度加入到数据头部
	return toWrite
}//制作数据报

func main() {
	serverAddr := SERVER_IP + ":" + strconv.Itoa(SERVER_PORT)//封装服务器IP+端口
	conn, err := net.Dial("udp", serverAddr)//运用dial
	checkError(err)
	defer conn.Close()

data, err := ioutil.ReadFile("D:/321.txt")//读取文件
checkError(err)

line := string(data)//将文件写入line字符串
lineLen := len(line)//计算文件长度
SERVER_RECV_LEN :=  1024*2//定义一次发送的数据报长度
toWrite := make([]string,lineLen/(SERVER_RECV_LEN-5)+1) //定义toWrite数组，元素为字符串
cnt := 0//定义数据报序号（在后面也是即将发送的数据报的序号）
N := 1 //定义窗口长度(在这里它也是cwnd)
ssthresh := 1024 //慢启动阈值
dupACKcount := 0//定义重复收到的同一个ACK报的数量
base := 0//定义基序号（已经确认收到的数据报序号）
msg := make([]byte, 10)  //创建msg以接收服务器数据报
timer := time.NewTimer(timesum)//定义定时器
for written := 0; written < lineLen; written += (SERVER_RECV_LEN-5) {
	if lineLen-written > (SERVER_RECV_LEN - 5) {
		toWrite[cnt] = line[written : written+(SERVER_RECV_LEN-5)]
		toWrite[cnt] = makedata(toWrite[cnt], cnt) //制作数据报
	} else {
		toWrite[cnt] = line[written:]
		toWrite[cnt] = makedata(toWrite[cnt], cnt) //制作数据报
	}
	cnt++
}//将文件读取到toWrite[cnt]并制作数据报
max := cnt-1;//定义数据报组最大量
cnt = 0//重置序号

go func() {
	for base <= max{//判断线程是否应该关闭
		_, err = conn.Read(msg) //接收ACK报
		checkError(err)
		strmsg := string(msg)//将数据报转化为字符串格式
		if int(strmsg[0]) == base && strmsg[1:3] == "OK" {//判断：接收到正确的数据报
			base += 1 //base+1
			if N < ssthresh/2 && dupACKcount <= 3 {
				N *=2 //慢启动的窗口增加
			} else if N >= ssthresh/2 && dupACKcount < 3 {
				N += 1 //拥塞避免的窗口增加（本来这里的1应该是1/N）
			} else if N >= ssthresh/2 && dupACKcount >= 3 {
				N = ssthresh  //从快速恢复进入到拥塞避免
			}
			dupACKcount = 0//重置 收到重复ACK报 的数值
			timer.Reset(timesum)//重置定时器
		}else if int(strmsg[0]) == base&& strmsg[1:3] == "NO" {//判断：接收到错误的数据报
			   cnt = base //立刻重新发送位置为base的报
		}else if int(strmsg[0]) == base {//判断：服务器未接收到base的数据报而超时发出的ACK报
			dupACKcount += 1 //收到重复ACK报的数量+1
			if dupACKcount == 3 { //判断是否进入快速恢复线程
				ssthresh = N/2 //重置慢启动阈值
				N = ssthresh + 3  //重置窗口长度
			}else if dupACKcount>3 {  //进入到快速恢复
				N += 1 //快速回复窗口增加
			}
		}
	}
}()//接受ACK报线程

go func() {
	for base<=max {//判断线程是否应该关闭
		<-timer.C //定时器
		cnt = base  //定时器超时更改cnt为base
		ssthresh = N/2 //更新慢启动阈值
		N = 1  //重置cwnd
		dupACKcount = 0 // 重置收到重复ACK报的数值
		timer.Reset(timesum) //重置定时器
	}
	timer.Stop() //关闭定时器
}()//超时处理线程

for base <= max {
	if cnt < base+N && cnt <= max { //判断是否可以发送
		_, err = conn.Write([]byte(toWrite[cnt])) //发送数据报
		checkError(err)
		cnt++ //即将发送的数据报序号+1
	}
}//发送数据报
_,err = conn.Write([]byte("over"))//发送终止信号
}
```

