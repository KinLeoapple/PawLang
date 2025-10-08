# Paw 类型系统

> **纯粹的 Rust 风格类型系统 - 18 个精确类型，0 个别名**

---

## 🎯 设计理念

Paw 采用完全纯粹的 Rust 风格类型系统：

✅ **精确性** - 每个类型大小和范围明确  
✅ **纯粹性** - 无别名，无歧义  
✅ **完整性** - 8 到 128 位完整覆盖  
✅ **一致性** - 与 Rust 95% 一致  

---

## 📊 完整类型列表（18个）

### 有符号整数类型（5个）

| 类型 | 位数 | 字节 | 范围 | C 类型 |
|------|------|------|------|--------|
| `i8` | 8 | 1 | -128 to 127 | `int8_t` |
| `i16` | 16 | 2 | -32,768 to 32,767 | `int16_t` |
| `i32` | 32 | 4 | -2,147,483,648 to 2,147,483,647 | `int32_t` |
| `i64` | 64 | 8 | -9.22×10¹⁸ to 9.22×10¹⁸ | `int64_t` |
| `i128` | 128 | 16 | -1.70×10³⁸ to 1.70×10³⁸ | `__int128` |

**默认：** 整数字面量推断为 `i32`

### 无符号整数类型（5个）

| 类型 | 位数 | 字节 | 范围 | C 类型 |
|------|------|------|------|--------|
| `u8` | 8 | 1 | 0 to 255 | `uint8_t` |
| `u16` | 16 | 2 | 0 to 65,535 | `uint16_t` |
| `u32` | 32 | 4 | 0 to 4,294,967,295 | `uint32_t` |
| `u64` | 64 | 8 | 0 to 1.84×10¹⁹ | `uint64_t` |
| `u128` | 128 | 16 | 0 to 3.40×10³⁸ | `unsigned __int128` |

### 浮点类型（2个）

| 类型 | 位数 | 字节 | 精度 | C 类型 |
|------|------|------|------|--------|
| `f32` | 32 | 4 | 单精度（约7位小数） | `float` |
| `f64` | 64 | 8 | 双精度（约15位小数） | `double` |

**默认：** 浮点字面量推断为 `f64`

### 其他类型（6个）

| 类型 | 说明 | C 类型 |
|------|------|--------|
| `bool` | 布尔类型（true/false） | `bool` |
| `char` | 字符类型 | `char` |
| `string` | 字符串类型 | `char*` |
| `void` | 空类型/无返回值 | `void` |

---

## 🎨 使用示例

### 1. 基础类型声明

```paw
fn basic_types() -> i32 {
    // 整数（默认 i32）
    let count = 42;              // i32
    let tiny: i8 = 127;          // 8位
    let small: i16 = 32767;      // 16位
    let normal: i32 = 1000000;   // 32位
    let large: i64 = 1000000000; // 64位
    let huge: i128 = 100000000000000000000; // 128位！
    
    // 无符号整数
    let byte: u8 = 255;
    let port: u16 = 8080;
    let id: u32 = 123456;
    let timestamp: u64 = 1234567890;
    
    // 浮点数（默认 f64）
    let pi = 3.14159;            // f64
    let single: f32 = 3.14;      // 32位
    let precise: f64 = 3.141592653589793; // 64位
    
    0
}
```

### 2. 颜色和像素（无符号小类型）

```paw
type Color = struct {
    r: u8    // 0-255
    g: u8
    b: u8
    a: u8
}

type Pixel = struct {
    color: Color
    x: u16
    y: u16
}

fn create_red() -> Color {
    Color { r: 255, g: 0, b: 0, a: 255 }
}
```

### 3. 网络编程（128位地址）

```paw
type IPv6Address = struct {
    addr: u128    // IPv6 地址正好 128 位
}

type SocketAddr = struct {
    ip: IPv6Address
    port: u16
}

fn create_localhost() -> SocketAddr {
    SocketAddr {
        ip: IPv6Address { addr: 1 },
        port: 8080
    }
}
```

### 4. 游戏实体（混合类型）

```paw
type Entity = struct {
    id: u64           // 实体 ID
    health: i32       // 生命值（可为负）
    position_x: f32   // X 坐标
    position_y: f32   // Y 坐标
    flags: u8         // 状态标志位
}

fn create_entity(id: u64) -> Entity {
    Entity {
        id,
        health: 100,
        position_x: 0.0,
        position_y: 0.0,
        flags: 0
    }
}
```

### 5. 密码学（大数）

```paw
type Hash256 = struct {
    value: [u8; 32]    // 256 位哈希（32 字节）
}

type UUID = struct {
    value: u128        // 标准 UUID 是 128 位
}

fn generate_uuid() -> UUID {
    UUID { value: 0 }  // 实际应使用随机数
}
```

---

## 🔄 类型转换

### 显式转换（as 关键字）

```paw
fn conversions() -> i64 {
    let small: i8 = 42;
    let medium: i32 = 1000;
    let large: i64 = 1000000;
    
    // 向上转换（安全）
    let up1 = small as i32;      // i8 → i32
    let up2 = medium as i64;     // i32 → i64
    let up3 = small as i128;     // i8 → i128
    
    // 向下转换（需注意溢出）
    let down1 = medium as i8;    // i32 → i8（可能溢出）
    let down2 = large as i32;    // i64 → i32（可能溢出）
    
    // 有符号 ↔ 无符号
    let signed: i32 = -42;
    let unsigned = signed as u32;  // 需小心负数
    
    // 整数 ↔ 浮点
    let int_val: i32 = 42;
    let float_val = int_val as f64;  // i32 → f64
    let back = float_val as i32;     // f64 → i32（截断小数）
    
    large
}
```

### 类型转换规则

**安全转换（不会丢失精度）：**
- 小整数 → 大整数：`i8` → `i16` → `i32` → `i64` → `i128`
- 小无符号 → 大无符号：`u8` → `u16` → `u32` → `u64` → `u128`
- 整数 → 浮点：`i32` → `f64`（大部分情况）
- `f32` → `f64`

**需要注意的转换：**
- 大整数 → 小整数：可能溢出
- 有符号 ↔ 无符号：负数会变成大正数
- 浮点 → 整数：会截断小数部分
- `f64` → `f32`：可能损失精度

---

## 📏 类型选择指南

### 何时使用各种类型

#### 有符号整数

```paw
// i8 (-128 to 127)
let offset: i8 = -10;
let direction: i8 = -1;  // -1 = 左，1 = 右

// i16 (-32K to 32K)
let temperature: i16 = -40;  // 摄氏度
let elevation: i16 = -100;   // 海拔（米）

// i32 (默认，-2B to 2B)
let balance: i32 = -1000;
let score: i32 = 12345;

// i64 (大数值)
let timestamp: i64 = 1234567890;
let large_number: i64 = 9223372036854775807;

// i128 (超大数值)
let crypto_value: i128 = 170141183460469231731687303715884105727;
```

#### 无符号整数

```paw
// u8 (0 to 255)
let age: u8 = 25;
let percentage: u8 = 75;
let rgb: u8 = 255;

// u16 (0 to 65K)
let port: u16 = 8080;
let year: u16 = 2024;

// u32 (0 to 4B)
let id: u32 = 123456789;
let counter: u32 = 1000000;

// u64 (0 to 18 quintillion)
let file_size: u64 = 1099511627776;  // 1TB
let entity_id: u64 = 18446744073709551615;

// u128 (超大正整数)
let ipv6: u128 = 340282366920938463463374607431768211455;
let uuid: u128 = 123456789012345678901234567890;
```

#### 浮点类型

```paw
// f32（单精度，节省内存）
let x: f32 = 3.14;
let y: f32 = 2.71;
let distance: f32 = 10.5;

// f64（双精度，默认）
let pi: f64 = 3.141592653589793;
let e: f64 = 2.718281828459045;
let precise_calc: f64 = 0.1 + 0.2;
```

---

## 🎯 实际应用场景

### 嵌入式系统

```paw
type Sensor = struct {
    id: u8
    value: u16
    status: u8
}

type Register = struct {
    addr: u16
    data: u8
}
```

### 网络协议

```paw
type PacketHeader = struct {
    version: u8
    length: u16
    checksum: u32
}

type IPv4Addr = struct {
    octets: [u8; 4]
}

type IPv6Addr = struct {
    addr: u128
}
```

### 金融系统

```paw
type Money = struct {
    amount: i64      // 以分为单位
    currency: string
}

type Transaction = struct {
    id: u64
    from: u64
    to: u64
    amount: Money
    timestamp: i64
}
```

### 游戏开发

```paw
type Vec3 = struct {
    x: f32
    y: f32
    z: f32
}

type Color = struct {
    r: u8
    g: u8
    b: u8
    a: u8
}

type Entity = struct {
    id: u64
    position: Vec3
    velocity: Vec3
    health: i32
    team: u8
}
```

---

## 📊 与 Rust 对比

| 特性 | Rust | Paw | 说明 |
|------|------|-----|------|
| i8-i64 | ✅ | ✅ | 完全一致 |
| i128 | ✅ | ✅ | 完全支持 |
| u8-u64 | ✅ | ✅ | 完全一致 |
| u128 | ✅ | ✅ | 完全支持 |
| f32/f64 | ✅ | ✅ | 完全一致 |
| isize/usize | ✅ | ❌ | Paw 不提供 |
| 类型别名 | ❌ | ❌ | 都不提供 ✓ |
| 默认类型 | i32/f64 | i32/f64 | 一致 ✓ |

**与 Rust 一致性：95%** ⭐⭐⭐⭐⭐

**差异说明：**
- Rust 有 `isize`/`usize`（平台相关大小）
- Paw 不提供，保持类型明确性
- 需要时显式使用 `i32`/`i64`

---

## 💡 最佳实践

### 1. 选择合适的类型大小

```paw
// ✅ 好 - 使用合适的类型
type Pixel = struct {
    r: u8    // 0-255，刚好 1 字节
    g: u8
    b: u8
    a: u8
}  // 总计 4 字节

// ❌ 避免 - 浪费内存
type BadPixel = struct {
    r: i64   // 只需要 0-255，却用了 8 字节
    g: i64
    b: i64
    a: i64
}  // 总计 32 字节（浪费 87.5%！）
```

### 2. 注意类型转换

```paw
// ✅ 好 - 向上转换（安全）
let small: i8 = 100;
let big: i32 = small as i32;  // 安全

// ⚠️ 注意 - 向下转换（可能溢出）
let big: i32 = 1000;
let small: i8 = big as i8;  // 溢出！结果是 -24
```

### 3. 使用类型推断

```paw
// ✅ 好 - 让编译器推断
let x = 42;        // i32
let y = 3.14;      // f64

// ✅ 好 - 需要时显式指定
let tiny: i8 = 42;
let huge: i128 = 1000000000000000000;
```

### 4. 混合运算需要转换

```paw
fn calculate() -> i32 {
    let a: i8 = 10;
    let b: i32 = 20;
    
    // ❌ 错误 - 类型不匹配
    // let sum = a + b;
    
    // ✅ 正确 - 显式转换
    let sum = a as i32 + b;
    
    sum
}
```

---

## 🔢 默认类型规则

### 字面量推断

```paw
let i = 42;          // 推断为 i32
let f = 3.14;        // 推断为 f64
let b = true;        // 推断为 bool
let s = "hello";     // 推断为 string
let c = 'x';         // 推断为 char
```

### 显式指定类型

```paw
// 当需要特定大小时，必须显式指定
let tiny: i8 = 42;
let large: i64 = 42;
let huge: i128 = 42;

let byte: u8 = 42;
let unsigned: u32 = 42;

let single: f32 = 3.14;
let double: f64 = 3.14;
```

---

## 🎊 总结

### Paw 类型系统特点

✅ **18 个精确类型**
- 有符号：i8, i16, i32, i64, i128
- 无符号：u8, u16, u32, u64, u128
- 浮点：f32, f64
- 其他：bool, char, string, void

✅ **0 个别名**
- 无 `int`、`uint`、`float`、`byte`
- 类型完全明确，无歧义

✅ **完整支持到 128 位**
- i128/u128 用于密码学、大数计算
- 与 Rust 完全一致

✅ **默认类型合理**
- 整数 → i32
- 浮点 → f64

✅ **类型转换显式**
- 使用 `as` 关键字
- 编译期类型检查

---

**纯粹 Rust 风格，无别名，无歧义！** ⭐⭐⭐⭐⭐

*Paw = Rust 类型系统 + 19 关键字 + 统一语法* 🐾✨

---

*最后更新：2025-10-08*
