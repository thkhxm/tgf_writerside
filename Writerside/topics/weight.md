# 权重工具
> 通用权重工具,可以简单的实现例如抽奖,随机命中等常规功能
## 案例

```Go
package util_test

import (
	"github.com/thkhxm/tgf/util"
	"testing"
)

//***************************************************
//@Link  https://github.com/thkhxm/tgf
//@Link  https://gitee.com/timgame/tgf
//@QQ群 7400585
//author tim.huang<thkhxm@gmail.com>
//@Description
//2023/11/29
//***************************************************

func Test_weightOperation_Roll(t *testing.T) {
	builder := util.NewWeightBuilder[int32]().Seed(1001)
	builder.AddWeight(10, 10, 1)
	builder.AddWeight(20, 10, 2)
	builder.AddWeight(30, 1, 3)
	builder.AddWeight(40, 10, 4)
	builder.AddWeight(50, 10, 5)
	w := builder.Build()
	for i := 0; i < 5; i++ {
		t.Logf("roll number : %d", w.Roll())
	}
	
	//=== RUN   Test_weightOperation_Roll
	//weight_test.go:26: roll number : &{50 5 9}
	//weight_test.go:26: roll number : &{30 3 0}
	//weight_test.go:26: roll number : &{50 5 8}
	//weight_test.go:26: roll number : &{40 4 9}
	//weight_test.go:26: roll number : &{40 4 8}
	//--- PASS: Test_weightOperation_Roll (0.00s)
	
}
```
根据以上代码我们可以看到,该权重工具使用了泛型,支持any数据类型,提供了随机种子的设置,常规的操作为以下接口

```Go
type IWeightData[T any] interface {
	Data() T
	Amount() int32
	Ratio() int32
	BaseRatio() int32
}

type IWeight[T any] interface {
	// Roll
	// @Description: 根据权重随机出一个数据,并且减少物品的数量
	// @return T
	//
	Roll() (res IWeightData[T])
	// AllItem
	// @Description: 这里不会进行切片的拷贝,所以在使用的时候需要注意
	// @return IWeightItem[T]
	//
	AllItem() []IWeightData[T]
}

type IWeightBuilder[T any] interface {
	AddWeight(weightRatio, amount int32, data T) IWeightBuilder[T]
	Build() IWeight[T]
	Seed(seed uint64) IWeightBuilder[T]
}
```

在使用的时候,我们首先调用实例化一个Builder,调用`AddWeight`添加权重项, 之后调用Build函数,实例化一个权重实例,这个实例使用`IWeight`接口,所以我们可以直接调用Roll函数进行随机命中,当我们命中其中的项目时
会自动对数量做递减,并且如果数量为0的时候,不会再命中.最后会返回一个`IWeightData`的数据,我们可以根据这个数据获得相关的基础数据.

> 如果需要无限数量,amount可以传-1

> 该工具不支持并发安全操作,如果需要并发安全,请在外部调用中自行通过channel或者其他方式实现

