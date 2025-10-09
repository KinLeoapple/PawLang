# PawLang 下一步规划

**当前版本**: v0.1.4-dev  
**当前分支**: 0.1.4  
**LLVM 集成**: ✅ 完成

---

## 📊 当前状态评估

### ✅ v0.1.4 已完成的功能

1. **LLVM 集成** (100% 完成)
   - ✅ 本地 LLVM 19.1.6 工具链
   - ✅ 自定义 C API 绑定 (400+ 行)
   - ✅ 三个后端 (C, LLVM Text, LLVM Native)
   - ✅ Zig 构建系统集成
   - ✅ 完整文档和脚本
   - ✅ 所有测试通过

2. **编译器核心** (v0.1.3 遗留)
   - ✅ 自动类型推断
   - ✅ 泛型系统 (函数 + 结构体)
   - ✅ 模块系统
   - ✅ 三种后端

3. **工具链**
   - ✅ C 编译 (本地 Clang 优先)
   - ✅ LLVM IR 生成 (两种模式)
   - ✅ 内嵌 TCC (快速开发)

### ⚠️ 已知问题

1. **内存泄漏** (优先级: 高)
   - 位置: `src/parser.zig` - `toOwnedSlice()` 调用
   - 影响: 编译器自身，不影响生成代码
   - 严重性: 开发时可见，但不影响功能
   - 修复难度: 中等

2. **LLVM 后端功能不完整** (优先级: 中)
   - 缺少: 控制流 (if/else, while, for)
   - 缺少: 字符串操作
   - 缺少: 数组/结构体
   - 当前: 只支持基础算术和函数调用

3. **文档需要更新** (优先级: 低)
   - 主 README 未提及 LLVM
   - 需要添加性能对比
   - 需要用户指南更新

---

## 🎯 下一步选项分析

### 选项 A: 完善 v0.1.4 并正式发布 (推荐⭐)

**时间估计**: 1-2 周  
**优先级**: 高  
**理由**: 当前功能已经很强大，但需要打磨

#### A1. 修复内存泄漏 (3-5 天)

**任务清单**:
1. 分析 `parser.zig` 中的内存分配
2. 识别未释放的 `toOwnedSlice()` 调用
3. 实现正确的生命周期管理
4. 添加内存测试
5. 验证无泄漏

**预期收益**:
- 干净的编译器输出
- 更好的用户体验
- 生产环境可用

#### A2. 完善 LLVM 后端 (5-7 天)

**任务清单**:
1. **控制流支持**
   - if/else 语句
   - while 循环
   - for 循环
   - break/continue

2. **数据结构**
   - 字符串字面量
   - 数组操作
   - 结构体字段访问

3. **高级特性**
   - 函数指针
   - 闭包 (基础)
   - 方法调用

**预期收益**:
- LLVM 后端可用于真实项目
- 完整的语言特性支持
- 与 C 后端功能对等

#### A3. 文档和发布 (2-3 天)

**任务清单**:
1. 更新主 README
2. 添加 LLVM 使用指南
3. 性能基准测试
4. 编写发布说明
5. 创建 GitHub Release
6. 打 v0.1.4 标签

**预期收益**:
- 专业的项目形象
- 用户易于上手
- 明确的功能说明

---

### 选项 B: 新语言特性开发

**时间估计**: 2-4 周  
**优先级**: 中  

#### B1. 模式匹配 (Pattern Matching)

```paw
// 目标语法
match value {
    Some(x) => println("Got: {}", x),
    None => println("Nothing"),
}

match point {
    Point { x: 0, y: 0 } => println("Origin"),
    Point { x, y } => println("Point at ({}, {})", x, y),
}
```

**实现要点**:
- AST 扩展 (MatchExpr)
- 穷尽性检查
- 模式绑定
- 三个后端生成

**收益**: 现代化语言特性

#### B2. 闭包支持

```paw
// 目标语法
let add_n = |n: i32| -> impl Fn(i32) -> i32 {
    return |x: i32| -> i32 { return x + n; };
};

let add_5 = add_n(5);
println("{}", add_5(10)); // 15
```

**实现要点**:
- 闭包捕获分析
- 环境对象生成
- 生命周期管理
- LLVM 函数指针

**收益**: 函数式编程支持

#### B3. 异步/并发

```paw
// 目标语法
async fn fetch_data(url: String) -> Result<Data, Error> {
    let response = await http.get(url);
    return response.json();
}

fn main() {
    let future = fetch_data("https://api.example.com");
    let data = await future;
}
```

**实现要点**:
- async/await 语法
- Future 类型
- 运行时调度器
- 协程支持

**收益**: 现代化并发模型

---

### 选项 C: 开发者工具

**时间估计**: 3-6 周  
**优先级**: 中

#### C1. Language Server Protocol (LSP)

**功能**:
- 语法高亮
- 代码补全
- 跳转定义
- 查找引用
- 错误诊断
- 重命名重构

**收益**:
- VS Code / Cursor 完整支持
- 提升开发体验
- 吸引更多用户

#### C2. 调试器集成

**功能**:
- LLDB/GDB 集成
- 断点调试
- 变量查看
- 调用栈

**收益**:
- 专业开发工具
- 简化调试流程

#### C3. REPL (Read-Eval-Print-Loop)

**功能**:
```bash
$ pawc repl
>>> let x = 42
>>> x + 8
50
>>> fn double(n: i32) -> i32 { return n * 2; }
>>> double(21)
42
```

**收益**:
- 快速原型开发
- 学习友好
- 交互式探索

---

### 选项 D: 性能和优化

**时间估计**: 2-3 周  
**优先级**: 低 (功能优先)

#### D1. 编译器性能优化

**改进点**:
- 并行词法/语法分析
- 增量编译
- 缓存中间结果
- 优化内存分配

**预期提升**:
- 编译速度: 2-5x
- 内存使用: -30%

#### D2. LLVM 优化 Pass 集成

**功能**:
```bash
pawc hello.paw --backend=llvm-native -O3
# 启用 LLVM 优化 Pass
```

**优化选项**:
- -O0: 无优化
- -O1: 基础优化
- -O2: 标准优化
- -O3: 激进优化
- -Os: 大小优化

**预期提升**:
- 运行速度: 1.5-3x
- 二进制大小: -20% (Os)

---

## 💡 推荐路线

### 🏆 最佳路线: A → B1 → C1

#### Phase 1: v0.1.4 完善 (2 周)
```
Week 1: 修复内存泄漏 + LLVM 控制流
Week 2: 字符串/数组支持 + 文档 + 发布
```

**成果**: 稳定的 v0.1.4 正式版

#### Phase 2: v0.1.5 模式匹配 (2-3 周)
```
Week 1-2: 模式匹配 AST 和类型检查
Week 3: 后端实现 (C + LLVM)
```

**成果**: 现代化的模式匹配系统

#### Phase 3: v0.2.0 LSP (3-4 周)
```
Week 1: LSP 基础架构
Week 2-3: 核心功能 (补全、跳转、诊断)
Week 4: VS Code 扩展
```

**成果**: 完整的 IDE 支持

---

## 📋 详细任务分解 (选项 A)

### Sprint 1: 内存管理 (5 天)

**Day 1-2: 分析和诊断**
- [ ] 运行 Valgrind/ASAN 分析
- [ ] 识别所有泄漏点
- [ ] 创建测试用例

**Day 3-4: 修复实现**
- [ ] 修复 parser.zig 泄漏
- [ ] 修复 llvm_backend.zig 泄漏
- [ ] 添加 defer 清理

**Day 5: 验证**
- [ ] 运行内存测试
- [ ] 确认无泄漏
- [ ] 性能基准测试

### Sprint 2: LLVM 控制流 (7 天)

**Day 1-2: if/else**
- [ ] AST 已支持，添加 LLVM 生成
- [ ] LLVMBuildCondBr
- [ ] 基本块管理
- [ ] 测试用例

**Day 3-4: while 循环**
- [ ] 循环基本块
- [ ] 条件检查
- [ ] break/continue
- [ ] 测试用例

**Day 5-6: for 循环**
- [ ] 迭代器支持
- [ ] 范围循环
- [ ] 集合迭代
- [ ] 测试用例

**Day 7: 集成测试**
- [ ] 复杂控制流测试
- [ ] 嵌套结构
- [ ] 边界情况

### Sprint 3: 数据结构 (5 天)

**Day 1-2: 字符串**
- [ ] 字符串字面量
- [ ] 字符串拼接
- [ ] 格式化输出
- [ ] LLVM 全局字符串

**Day 3-4: 数组**
- [ ] 数组字面量
- [ ] 索引访问
- [ ] 长度获取
- [ ] LLVM 数组类型

**Day 5: 结构体**
- [ ] 结构体字面量
- [ ] 字段访问
- [ ] 方法调用
- [ ] LLVM 结构体类型

### Sprint 4: 文档和发布 (3 天)

**Day 1: 文档**
- [ ] 更新 README.md
- [ ] 完善 LLVM 使用指南
- [ ] 性能对比数据
- [ ] 迁移指南

**Day 2: 测试和打包**
- [ ] 完整回归测试
- [ ] 性能基准测试
- [ ] 打包发布文件
- [ ] 编写发布说明

**Day 3: 发布**
- [ ] 合并到 main
- [ ] 创建 v0.1.4 标签
- [ ] GitHub Release
- [ ] 更新文档站点

---

## 🎨 具体实现示例

### 内存泄漏修复示例

**问题代码** (parser.zig):
```zig
.type_args = try type_args.toOwnedSlice(),  // 泄漏!
```

**修复方案**:
```zig
// 方案 1: 添加到 arena allocator
const arena = std.heap.ArenaAllocator.init(allocator);
defer arena.deinit();
// 所有 AST 节点用 arena

// 方案 2: 显式管理
defer {
    for (type_args.items) |arg| {
        allocator.free(arg);
    }
    allocator.free(type_args.items);
}
```

### LLVM 控制流实现示例

**if/else 生成**:
```zig
fn generateIf(self: *LLVMNativeBackend, if_stmt: ast.IfStmt) !void {
    // 1. 生成条件表达式
    const cond = try self.generateExpr(if_stmt.condition);
    
    // 2. 创建基本块
    const then_block = llvm.appendBasicBlock(self.context, self.current_func, "if.then");
    const else_block = llvm.appendBasicBlock(self.context, self.current_func, "if.else");
    const cont_block = llvm.appendBasicBlock(self.context, self.current_func, "if.cont");
    
    // 3. 构建条件分支
    _ = self.builder.buildCondBr(cond, then_block, else_block);
    
    // 4. then 分支
    self.builder.positionAtEnd(then_block);
    try self.generateStmt(if_stmt.then_branch);
    _ = self.builder.buildBr(cont_block);
    
    // 5. else 分支
    self.builder.positionAtEnd(else_block);
    if (if_stmt.else_branch) |else_br| {
        try self.generateStmt(else_br);
    }
    _ = self.builder.buildBr(cont_block);
    
    // 6. 继续
    self.builder.positionAtEnd(cont_block);
}
```

---

## 📈 优先级矩阵

| 任务 | 重要性 | 紧急性 | 难度 | 推荐优先级 |
|------|--------|--------|------|-----------|
| 修复内存泄漏 | 高 | 中 | 中 | 🔴 P0 |
| LLVM 控制流 | 高 | 高 | 中 | 🔴 P0 |
| 字符串支持 | 高 | 中 | 低 | 🟡 P1 |
| 更新文档 | 中 | 高 | 低 | 🟡 P1 |
| 数组支持 | 中 | 低 | 中 | 🟢 P2 |
| 模式匹配 | 低 | 低 | 高 | 🟢 P2 |
| LSP | 低 | 低 | 高 | ⚪ P3 |
| REPL | 低 | 低 | 中 | ⚪ P3 |

---

## 🗓 推荐时间表

### 2周冲刺计划 (v0.1.4 正式版)

```
Week 1 (Oct 10-16):
├─ Day 1-2: 内存泄漏诊断和修复
├─ Day 3-4: LLVM if/else 实现
├─ Day 5-6: LLVM while 循环
└─ Day 7: 周末测试

Week 2 (Oct 17-23):
├─ Day 1-2: LLVM for 循环
├─ Day 3: 字符串支持
├─ Day 4: 完整测试
├─ Day 5: 文档更新
├─ Day 6: 发布准备
└─ Day 7: 正式发布 v0.1.4 🚀
```

### 之后 (v0.1.5+)

```
Nov 2025: v0.1.5 - 模式匹配
Dec 2025: v0.1.6 - 闭包支持  
Jan 2026: v0.2.0 - LSP + 主版本升级
```

---

## 💻 开发环境建议

### 内存调试工具

```bash
# macOS
brew install valgrind  # 或使用 leaks
leaks --atExit -- ./zig-out/bin/pawc test.paw

# Linux
valgrind --leak-check=full ./zig-out/bin/pawc test.paw

# Zig 内置
zig build -Doptimize=Debug
# GeneralPurposeAllocator 自动检测
```

### LLVM 调试工具

```bash
# 验证 IR
llvm/install/bin/opt -verify output.ll

# 查看优化效果
llvm/install/bin/opt -O3 -print-after-all output.ll

# 查看生成的汇编
llvm/install/bin/llc output.ll -o output.s
cat output.s
```

---

## 🎓 学习资源

### LLVM 控制流
- [LLVM Language Reference - Terminator Instructions](https://llvm.org/docs/LangRef.html#terminator-instructions)
- [Kaleidoscope Tutorial - Control Flow](https://llvm.org/docs/tutorial/MyFirstLanguageFrontend/LangImpl05.html)

### 内存管理
- [Zig Memory Management](https://ziglang.org/documentation/master/#Memory)
- [Arena Allocator Pattern](https://ziglang.org/documentation/master/#ArenaAllocator)

### 编译器设计
- [Crafting Interpreters](https://craftinginterpreters.com/)
- [Modern Compiler Implementation](https://www.cs.princeton.edu/~appel/modern/)

---

## 🤔 决策建议

### 如果你想...

**快速看到成果** → 选择 **A (完善 v0.1.4)**
- 2 周即可发布
- 功能完整
- 用户可用

**探索新特性** → 选择 **B (新功能)**
- 更有趣
- 学习新概念
- 技术挑战

**改善开发体验** → 选择 **C (工具链)**
- 长期收益
- 提升效率
- 吸引贡献者

**追求极致性能** → 选择 **D (优化)**
- 性能提升明显
- 技术深度
- 竞争力

---

## 📝 我的建议

### 🎯 推荐: 选项 A - 完善 v0.1.4

**理由**:

1. **完整性**: 当前 LLVM 集成很强大，但不完整
2. **稳定性**: 修复内存泄漏提升质量
3. **可用性**: 添加控制流后就能写真实程序
4. **成就感**: 2 周后发布完整版本
5. **基础**: 为后续功能打好基础

**具体步骤**:

```bash
# Week 1
1. 修复内存泄漏
2. 实现 LLVM if/else
3. 实现 LLVM while 循环

# Week 2  
4. 实现 LLVM for 循环
5. 添加字符串支持
6. 完善文档
7. 发布 v0.1.4

# Result
✅ 三个完整后端
✅ 无内存泄漏
✅ 可以写真实程序
✅ 专业的文档
```

---

## 🚀 立即可以开始的任务

### Quick Wins (< 1 天)

1. **更新 README**
   - 添加 LLVM 特性说明
   - 更新示例代码
   - 添加性能对比

2. **添加测试用例**
   - 控制流测试
   - 边界情况
   - 错误处理

3. **改进错误消息**
   - 更友好的提示
   - 颜色输出
   - 建议修复

### Medium Tasks (2-3 天)

1. **内存泄漏修复**
   - 使用 Arena Allocator
   - 或显式清理

2. **LLVM if/else**
   - 条件分支
   - 基本块管理

3. **性能基准**
   - 编译速度测试
   - 运行时性能
   - 内存使用

---

## ✅ 决策检查清单

在开始下一步之前，考虑：

- [ ] v0.1.4 的核心目标是否完成？ (✅ LLVM 集成完成)
- [ ] 当前代码质量是否满意？ (⚠️ 有内存泄漏)
- [ ] 是否需要发布稳定版？ (推荐 ✅)
- [ ] 用户最需要什么功能？ (控制流、字符串)
- [ ] 团队的兴趣点在哪里？ (由你决定!)

---

## 🎯 最终建议

**我建议: 选择 A - 完善 v0.1.4 并发布正式版**

**下一个任务: 修复内存泄漏**

**预期时间**: 2-3 天

**要开始吗？** 我可以立即帮你：
1. 分析内存泄漏
2. 实现修复方案
3. 添加测试验证

或者如果你有其他想法，我们也可以探索其他方向！

🐾 **你想选择哪个方向？**

