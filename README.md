# Anvil - Dot notation from Go type instance
- [Usage](#usage)
  - [As an anvil instance](#example-as-an-anvil-instance)
  - [As package static call](#example-as-package-static-call)
- [Modifier usage](#modifier-usage)
- [TODO List](#todo)

## Usage
### Example: As an `anvil` instance
```go
do := anvil.Anvil{Mode:anvil.NoSkip, Glue:"."}
items, _ := do.Notation(v)
```
```go
Item{Key:"Test.Embedded.Boolean", Value:true}
Item{Key:"Test.unexported", Value:"string_val"}
Item{Key:"Test.json_tag", Value:1}
Item{Key:"Test.PointerStr.F1", Value:interface {}(nil)}
Item{Key:"Test.PointerStr.F2", Value:interface {}(nil)}
Item{Key:"Test.Face", Value:interface {}(nil)}
Item{Key:"Test.digits.Int", Value:0}
Item{Key:"Test.digits.Int8", Value:-1}
Item{Key:"Test.digits.Int16", Value:-16}
Item{Key:"Test.digits.Int32", Value:-32}
Item{Key:"Test.digits.Int64", Value:-64}
Item{Key:"Test.digits.zero", Value:0x0}
Item{Key:"Test.digits.Uint8", Value:0x8}
Item{Key:"Test.digits.Uint16", Value:0x10}
Item{Key:"Test.digits.Uint32", Value:0x20}
Item{Key:"Test.digits.Uint64", Value:0x40}
Item{Key:"Test.digits.Float32", Value:0.32}
Item{Key:"Test.digits.Float64", Value:-0.64}
```

<details><summary>Full example</summary>
<p>

### Full example

```go
import (
	"fmt"
	"github.com/iveronanomi/anvil"
)

type (
	IFace interface {
		Name() interface{}
		Complex128() complex128
	}
	Embedded struct {
		Boolean bool
	}
	PointerStr struct {
		F1 []string
		F2 []Sliced
	}
	Sliced struct {
		Key   string
		Value interface{}
		Bool  *bool
	}
	Nested struct {
		*Nested
	}
	Digits struct {
		Int     int
		Int8    int8
		Int16   int16
		Int32   int32
		Int64   int64
		Uint    uint `json:"zero"`
		Uint8   uint8
		Uint16  uint16
		Uint32  uint32
		Uint64  uint64
		Float32 float32
		Float64 float64
	}
	Test struct {
		Embedded
		unexported string
		Pointer    *string
		Json       int8 `json:"json_tag"`
		PointerStr *PointerStr
		Time       time.Time
		Face       IFace `json:"-,"`
		digits     Digits
	}
)

func main() {
	v := Test{
		Embedded: Embedded{
			Boolean: true,
		},
		unexported: "string_val", // todo: check `json` tag behaviors
		Json:       1,
		PointerStr: &PointerStr{
			F2: []Sliced{},
		},
		digits: Digits{
			Int:     0,
			Int8:    -1,
			Int16:   -16,
			Int32:   -32,
			Int64:   -64,
			Uint:    0,
			Uint8:   8,
			Uint16:  16,
			Uint32:  32,
			Uint64:  64,
			Float32: .32,
			Float64: -.64,
		},
	}
	items, _ := anvil.Notation(v, anvil.Skip, ".")

	for i := range items {
		fmt.Printf("\n%#v", items[i])
	}
}
```
```go
Item{Key:"Test.Embedded.Boolean", Value:true}
Item{Key:"Test.unexported", Value:"string_val"}
Item{Key:"Test.json_tag", Value:1}
Item{Key:"Test.digits.Int8", Value:-1}
Item{Key:"Test.digits.Int16", Value:-16}
Item{Key:"Test.digits.Int32", Value:-32}
Item{Key:"Test.digits.Int64", Value:-64}
Item{Key:"Test.digits.Uint8", Value:0x8}
Item{Key:"Test.digits.Uint16", Value:0x10}
Item{Key:"Test.digits.Uint32", Value:0x20}
Item{Key:"Test.digits.Uint64", Value:0x40}
Item{Key:"Test.digits.Float32", Value:0.32}
Item{Key:"Test.digits.Float64", Value:-0.64}
```

</p>
</details>

### Example: As package static call
```go
items, _ := anvil.Notation(v, anvil.Skip, ".")
```
```go
Item{Key:"Test.Embedded.Boolean", Value:true}
Item{Key:"Test.unexported", Value:"string_val"}
Item{Key:"Test.json_tag", Value:1}
Item{Key:"Test.digits.Int8", Value:-1}
Item{Key:"Test.digits.Int16", Value:-16}
Item{Key:"Test.digits.Int32", Value:-32}
Item{Key:"Test.digits.Int64", Value:-64}
Item{Key:"Test.digits.Uint8", Value:0x8}
Item{Key:"Test.digits.Uint16", Value:0x10}
Item{Key:"Test.digits.Uint32", Value:0x20}
Item{Key:"Test.digits.Uint64", Value:0x40}
Item{Key:"Test.digits.Float32", Value:0.32}
Item{Key:"Test.digits.Float64", Value:-0.64}
```

## Modifier usage
```go
package main

import (
	"fmt"
	"time"

	"github.com/iveronanomi/anvil"
	"github.com/iveronanomi/anvil/modifier"
)

type (
	MyStruct struct {
		Field time.Time
	}
)

func main() {
	v := MyStruct{Field:time.Now()}

	do := anvil.Anvil{Mode:anvil.NoSkip, Glue:"."}
	do.Modifier(time.Now(), modifier.Time)
	items, _ := do.Notation(v)

	for i := range items {
		fmt.Printf("\n%#v", items[i])
	}
}

```
```go
Item{Key:"MyStruct.Field", Value:"2019-04-22T17:50:27.194752+03:00"}
```

### TODO
- [ ] add modifiers executions for all types
- [ ] optimize types itteration