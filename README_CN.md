## **C++ 编程规范**

### **1. 通用编程实践**
- **编写干净的代码**：
  - 使用具有意义和描述性的变量、函数和类名。
  - 避免使用魔法数字。用常量或 `constexpr` 替代。
  - 保持函数短小，专注于单一任务。

- **合理使用注释**：
  - 编写注释解释 *为什么* 要这样做，而不是 *怎么* 做。
  - 使用 `//` 写单行注释，使用 `/* ... */` 写多行注释。
  - 避免在明显的代码（例如简单 `for` 循环）中过度注释。

- **遵守代码标准**：
  - 项目中保持一致的 **缩进**（例如，4 个空格或 tab 键）。
  - 遵循既定的规范，例如 Google 的 C++ 编码规范或 MISRA C++（对于关键安全系统）。

---

### **2. 代码结构与组织**
- **将代码分为多个文件**：
  - 为头文件（`.h` 或 `.hpp`）和源文件（`.cpp`）使用独立文件。
  - 将相关类和函数组织到模块/命名空间中。

- **使用包含保护/`#pragma once`**：
  ```cpp
  // 使用 #pragma once 或
  #ifndef HEADER_FILE_NAME
  #define HEADER_FILE_NAME
  // 头文件内容
  #endif
  ```
  - 防止头文件被多次包含。

- **避免包含不必要的头文件**：
  - 尽量使用前向声明减少依赖。
  - 示例：
    ```cpp
    class MyClass;  // 前向声明
    ```

---

### **3. 面向对象编程 (OOP)**
- **封装**：
  - 将类的成员变量设为 `private` 或 `protected`。
  - 仅在必要时提供公共 `getter` 和 `setter` 方法。

  示例：
  ```cpp
  class Example {
  private:
      int data;
  public:
      void setData(int value) { data = value; }
      int getData() const { return data; }
  };
  ```

- **继承**：
  - 谨慎使用继承，遵守 **“is-a 关系”** 原则。
  - 偏向于 **组合优于继承**，以避免类之间的高度耦合。

- **在多态类中使用虚析构函数**：
  - 如果基类是多态的，应始终声明虚析构函数。
  ```cpp
  class Base {
  public:
      virtual ~Base() {}
  };
  ```

---

### **4. 使用现代 C++**
- **迁移到 C++11 或更高版本**：
  - 使用 **`auto`** 类型推导简化代码，前提是不会降低可读性。
  - 使用智能指针（`std::unique_ptr`, `std::shared_ptr`）代替原始指针进行内存管理。

  示例：
  ```cpp
  std::unique_ptr<MyClass> myObj = std::make_unique<MyClass>();
  ```

- **优先使用 `std::array`、`std::vector` 和 `std::string`**，避免使用原始数组和 C 风格字符串。
- 使用 **范围 for 循环**：
  ```cpp
  for (const auto& item : myVector) {
      std::cout << item << std::endl;
  }
  ```

---

### **5. 错误处理**
- **使用异常处理运行时错误**：
  - 不要将异常用于普通流程控制。
  ```cpp
  try {
      // 可能抛出异常的代码
  } catch (const std::exception& e) {
      std::cerr << "错误: " << e.what() << std::endl;
  }
  ```

- **避免在析构函数中抛出异常**：
  - 析构函数抛出异常可能导致未定义行为。

- **使用断言用于调试**：
  - 使用 `assert` 检查不会失败的条件。
  ```cpp
  #include <cassert>
  assert(x > 0 && "x 必须为正数");
  ```

---

### **6. 内存管理**
- **避免手动管理内存**：
  - 始终优先使用 **智能指针** 来管理对象所有权和自动清理。
  - 使用 `std::move` 和 `std::forward` 控制对象的所有权。

- **最小化动态分配对象**：
  - 尽量使用栈内存和自动变量提高性能。

- **RAII（资源获取即初始化）**：
  - 在构造函数中获取资源（如文件流、网络连接），在析构函数中释放资源。

  示例：
  ```cpp
  class FileHandler {
      std::fstream file;
  public:
      FileHandler(const std::string& fileName) { file.open(fileName); }
      ~FileHandler() { file.close(); }
  };
  ```

---

### **7. 并发编程**
- 优先使用 **C++ 标准线程和同步工具**：
  ```cpp
  std::thread t([] { std::cout << "线程运行中\n"; });
  t.join();
  ```

- 使用 **`std::mutex`** 进行线程同步。
- 对于无锁编程，考虑使用 **`std::atomic`**。

---

### **8. 命名空间**
- 使用命名空间封装代码防止名称冲突：
  ```cpp
  namespace MyProject {
      class MyClass {
          // 实现
      };
  }
  ```

- 避免在头文件中全局使用 `using namespace` 指令，建议使用限定名称。

---

### **9. 测试**
- 使用 **单元测试** 验证代码行为：
  - 可以选择测试框架，例如 **Google Test（gtest）** 或 **Catch2**。
- 为所有函数和边界条件编写测试用例，以确保可靠性。

---

### **10. 性能优化**
- **优化前先分析**：
  - 使用性能分析工具检测性能瓶颈。

- **使用移动语义**：
  - 避免不必要的拷贝，优先使用移动语义：
  ```cpp
  std::vector<int> v1 = {1, 2, 3};
  std::vector<int> v2 = std::move(v1);  // 所有权转移，避免拷贝。
  ```

- **内联小函数**：
  - 对于小且频繁调用的函数，可以使用 `inline` 减少开销。

---

### **11. 文档化**
- 为可读性和后续改进记录文档。
  - 使用 **Doxygen** 或类似工具生成结构化文档。
  - 解释函数行为、输入输出和前置条件。

  示例：
  ```cpp
  /**
   * @brief 添加两个整数。
   * 
   * @param a 第一个整数。
   * @param b 第二个整数。
   * @return a 和 b 的和。
   */
  int add(int a, int b);
  ```

---

### **12. 安全编码**
- **验证用户输入**：
  - 清理和验证所有用户输入以防止注入或缓冲区溢出攻击。

- **避免未定义行为**：
  - 确保数组边界检查。
  - 避免解引用空指针。

- **遵循安全编程实践**：
  - 对于固定大小的集合，使用 `std::array` 替代原始数组。
  - 使用静态分析工具检测潜在漏洞。


