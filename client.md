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

const (
	SERVER_IP       =  "10.14.124.84"
	SERVER_PORT     =  10006
	SERVER_RECV_LEN =  1024
	N = 4
	timesum = time.Millisecond
)//定义服务器的IP,端口，数据报长度，窗口长度
func CheckSum(data []byte) uint16 {
	var (
		sum    uint32
		length int = len(data)
		index  int
	)
	//以每16位为单位进行求和，直到所有的字节全部求完或者只剩下一个8位字节（如果剩余一个8位字节说明字节数为奇数个）
	for length > 1 {
		if uint32(data[index+1])==0&&uint32(data[index+2])==0{
			sum += uint32(data[index])
			index += 1
			break
		}
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
	serverAddr := SERVER_IP + ":" + strconv.Itoa(SERVER_PORT)//服务器IP+端口
	conn, err := net.Dial("udp", serverAddr)
	checkError(err)
	defer conn.Close()



data, err := ioutil.ReadFile("D:/321.txt")//读取文件
if err != nil {
		fmt.Println("File reading error", err)
		return
	}

line := string(data)//将文件写入line
lineLen := len(line)//计算文件长度
toWrite := make([]string,lineLen/(SERVER_RECV_LEN-5)+1) //定义toWrite数组，元素为字符串
cnt := 0//定义序号
base := 0//定义基序号
msg := make([]byte, 3)  //创建msg以接收服务器数据报
timer := time.NewTimer(timesum)//定义定时器
n := SERVER_RECV_LEN-5//定义每次可读取文件数据的字节
for written := 0; written < lineLen; written += n {
	if lineLen-written > (SERVER_RECV_LEN - 5) {
		toWrite[cnt] = line[written : written+(SERVER_RECV_LEN-5)]
	} else {
		toWrite[cnt] = line[written:]
	}
	cnt++
}//将文件读取到toWrite[cnt]

/*f, err := os.OpenFile("D:/GO_Workspace/test2.txt", os.O_APPEND|os.O_WRONLY,4000)//打开文件
if err != nil {
	fmt.Println(err)
	return
}
_, err = fmt.Fprint(f, toWrite)//写入文件
if err != nil {
	fmt.Println(err)
	f.Close()
	return
}
err = f.Close()//关闭文件
if err != nil {
	fmt.Println(err)
	return
}*/

max := cnt;//定义数据报组最大量
cnt = 0//重置序号
go func() {
	for base <= max{
		_, err = conn.Read(msg) //接收数据报
		checkError(err)
		strmsg := string(msg)//将数据报转化为字符串格式
		if int(strmsg[0]) != base || strmsg[1:3] != "OK" {
			fmt.Println(toWrite[base])
			base += 1
			timer.Reset(timesum)
		}
		timer.Stop()
	}
		}()
go func() {
	for base<=max {
		<-timer.C
		cnt = base
		timer.Reset(timesum)
	}
}()//计时器
for base<=max {
	if cnt < base+N {
		toWrite[cnt] = makedata(toWrite[cnt], cnt) //制作数据报
		n, err = conn.Write([]byte(toWrite[cnt]))  //发送数据报
		fmt.Println(toWrite[cnt])
		checkError(err)
		cnt++
	}
}
	}
```

