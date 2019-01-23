# tc-abi-go
迅雷链合约abi编码工具，go语言版本

## 使用方法
### 1、构造ABI对象
函数 `func JSON(reader io.Reader) (ABI, error)` 用于构造ABI对象，参数`reader`为合约的函数列表。
 示例：
```
const jsondata = `
[
	{ "type" : "function", "name" : "balance", "constant" : true },
	{ "type" : "function", "name" : "send", "constant" : false, "inputs" : [ { "name" : "amount", "type" : "uint256" } ] }
]`

abi, err := JSON(strings.NewReader(jsondata))
if err != nil {
	t.Error(err)
	t.FailNow()
}
```

### 2、生成合约调用ABI
ABI对象函数`func (abi ABI) Pack(name string, args ...interface{}) ([]byte, error)`用于生成ABI编码，参数`name`为调用的合约函数名，可变参数`args`为可选的合约调用参数。
示例：
```
if _, err := abi.Pack("balance", 1); err == nil {
	t.Error("expected error for balance(1)")
}
```

### 3、解码ABI
ABI对象函数`func (abi ABI) Unpack(v interface{}, name string, output []byte) (err error)`用于解码合约掉的返回，参数`v`为合约函数返回的数据结构，`name`为调用合约函数名，output为合约输出。
示例：
```
const hexdata = `000000000000000000000000376c47978271565f56deb45495afa69e59c16ab200000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000000000000000000060000000000000000000000000000000000000000000000000000000000000000158`
data, err := hex.DecodeString(hexdata)

type ReceivedEvent struct {
	Address common.Address
	Amount  *big.Int
	Memo    []byte
}
var ev ReceivedEvent

err = abi.Unpack(&ev, "received", data)
if err != nil {
	t.Error(err)
} else {
	t.Logf("len(data): %d; received event: %+v", len(data), ev)
}
```

### 参考示例
#### 接口实现详见 abi.go
#### 使用示例详见 abi_test.go