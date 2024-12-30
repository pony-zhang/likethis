
---

### **原子组件设计模板**

#### 1. **组件定义**
- **组件类型**：明确组件的类型（如按钮、输入框、标签等）。
- **功能描述**：简要描述组件的功能和用途。

#### 2. **Props定义**
- **外部数据**：通过`props`传递的数据，定义明确的类型。
- **可选参数**：标记哪些`props`是可选的，并提供默认值。
- **事件处理**：定义组件支持的事件（如`onClick`、`onChange`等）。

#### 3. **内部状态**
- **状态管理**：使用`useState`或`useReducer`管理组件内部状态。
- **状态类型**：明确状态的类型。

#### 4. **样式处理**
- **CSS-in-JS**：使用`styled-components`或`emotion`等库管理样式。
- **动态样式**：根据`props`或状态动态调整样式。

#### 5. **组合与复用**
- **子组件支持**：通过`children`支持嵌套内容。
- **组件复用**：确保组件足够通用，可以在不同场景中复用。

#### 6. **TypeScript类型**
- **Props类型**：使用`interface`或`type`定义`props`的类型。
- **事件类型**：为事件处理函数定义明确的类型。

#### 7. **导出与文档**
- **默认导出**：导出组件以便在其他地方使用。
- **文档注释**：为组件和`props`添加清晰的文档注释。

---

### **模板示例：按钮组件**

```tsx
import React from 'react';
import styled from 'styled-components';

// 1. Props定义
interface ButtonProps {
  /** 按钮文本 */
  children: React.ReactNode;
  /** 是否为主按钮 */
  primary?: boolean;
  /** 点击事件处理函数 */
  onClick?: () => void;
  /** 是否禁用 */
  disabled?: boolean;
}

// 2. 样式处理
const StyledButton = styled.button<ButtonProps>`
  background-color: ${(props) => (props.primary ? 'blue' : 'gray')};
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: ${(props) => (props.disabled ? 'not-allowed' : 'pointer')};
  opacity: ${(props) => (props.disabled ? 0.6 : 1)};

  &:hover {
    opacity: ${(props) => (props.disabled ? 0.6 : 0.8)};
  }
`;

// 3. 组件实现
const Button: React.FC<ButtonProps> = ({
  children,
  primary = false,
  disabled = false,
  onClick,
}) => {
  return (
    <StyledButton
      primary={primary}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </StyledButton>
  );
};

// 4. 默认导出
export default Button;
```

---

通过遵循这个模板，你可以设计出高质量的原子组件，为构建复杂的UI界面奠定坚实的基础。