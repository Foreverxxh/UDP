```
package main

import (
    "fmt"
    "net"
    "os"
    "strconv"
)

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
}//检验和算法
func Uint16ToBytes(n uint16) []byte {
    return []byte{
        byte(n),
        byte(n >> 8),
    }
}//uint16  ->   byte
func BytesToUint16(array []byte) uint16 {
    var data uint16 =0
    for i:=0;i< len(array);i++  {
        data = data+uint16(uint(array[i])<<uint(8*i))
    }

return data

}// byte  ->   uint16
func checkError(err error) {
    if err != nil {
        fmt.Println(err)
        os.Exit(1)
    }
}
const (
    SERVER_IP       =  "10.14.124.84"
    SERVER_PORT     =  10006
    SERVER_RECV_LEN =  1024*2
)//定义服务器的IP，端口，和数据长度

func main() {
    address := SERVER_IP + ":" + strconv.Itoa(SERVER_PORT)//服务器IP+端口
    addr, err := net.ResolveUDPAddr("udp", address)
    checkError(err)
    conn, err := net.ListenUDP("udp", addr)//开启listen
    checkError(err)

defer conn.Close()
cnt := 0//记录文件序号
ACK := string(cnt)+"OK"//定义ACK
data := make([]byte, SERVER_RECV_LEN)//定义接受数据的data
for {
    _, rAddr, err := conn.ReadFromUDP(data) //读取总数据
    checkError(err)
    strData := string(data) //将总数据转化为字符串
    strDatalen := BytesToUint16([]byte(strData[3:5]))//读取数据的长度
    abg := CheckSum([]byte(strData[5:5+strDatalen])) //重新计算数据的校验和

​    if abg == BytesToUint16([]byte(strData[1:3]))&&cnt == int(strData[0]){
​        f, err := os.OpenFile("D:/GO_Workspace/test2.txt", os.O_APPEND|os.O_WRONLY,4000)//打开文件
​        checkError(err)
​        _, err = fmt.Fprint(f, strData[5:5+strDatalen])//写入文件
​        checkError(err)
​        err = f.Close()//关闭文件
​        checkError(err)
​        ACK = string(cnt) + "OK"//更新ACK
​        _, err = conn.WriteToUDP([]byte(ACK), rAddr) //发送ACK
​        checkError(err)
​        cnt++
​    }else {
​            ACK = string(cnt) + "NO"//定义ACK
​            _,err = conn.WriteToUDP([]byte(ACK),rAddr)//发送ACK
​            checkError(err)
​    }
}

}
```


