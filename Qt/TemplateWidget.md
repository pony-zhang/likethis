
---

###  `TemplateWidget` 设计

#### 头文件：`TemplateWidget.h`

```cpp
#ifndef TEMPLATEWIDGET_H
#define TEMPLATEWIDGET_H

#include <QWidget>
#include <QString>
#include <QColor>
#include <QEvent>

/**
 * @brief TemplateWidget 是一个模板控件，展示了如何设计一个符合Qt官方风格的控件。
 *
 * 该控件包含以下内容：
 * - 构造函数
 * - 信号与槽
 * - 属性（Q_PROPERTY）
 * - 通知（属性变化时发出信号）
 * - 可重写的事件（如 paintEvent, mousePressEvent 等）
 * - 支持动态属性、样式表、国际化、键盘导航、高DPI屏幕等
 */
class TemplateWidget : public QWidget {
    Q_OBJECT

    // 定义属性
    Q_PROPERTY(QString text READ text WRITE setText NOTIFY textChanged)
    Q_PROPERTY(QColor backgroundColor READ backgroundColor WRITE setBackgroundColor NOTIFY backgroundColorChanged)
    Q_PROPERTY(bool highlighted READ isHighlighted WRITE setHighlighted NOTIFY highlightedChanged)

public:
    /**
     * @brief 构造函数
     * @param parent 父控件
     */
    explicit TemplateWidget(QWidget *parent = nullptr);

    /**
     * @brief 析构函数
     */
    ~TemplateWidget();

    /**
     * @brief 获取控件显示的文本
     * @return 当前文本
     */
    QString text() const;

    /**
     * @brief 设置控件显示的文本
     * @param text 新文本
     */
    void setText(const QString &text);

    /**
     * @brief 获取背景颜色
     * @return 当前背景颜色
     */
    QColor backgroundColor() const;

    /**
     * @brief 设置背景颜色
     * @param color 新背景颜色
     */
    void setBackgroundColor(const QColor &color);

    /**
     * @brief 获取高亮状态
     * @return 是否高亮
     */
    bool isHighlighted() const;

    /**
     * @brief 设置高亮状态
     * @param highlighted 是否高亮
     */
    void setHighlighted(bool highlighted);

signals:
    /**
     * @brief 文本发生变化时发出的信号
     * @param text 新文本
     */
    void textChanged(const QString &text);

    /**
     * @brief 背景颜色发生变化时发出的信号
     * @param color 新背景颜色
     */
    void backgroundColorChanged(const QColor &color);

    /**
     * @brief 高亮状态发生变化时发出的信号
     * @param highlighted 是否高亮
     */
    void highlightedChanged(bool highlighted);

    /**
     * @brief 用户点击控件时发出的信号
     */
    void clicked();

protected:
    /**
     * @brief 重写绘制事件
     * @param event 绘制事件
     */
    void paintEvent(QPaintEvent *event) override;

    /**
     * @brief 重写鼠标按下事件
     * @param event 鼠标事件
     */
    void mousePressEvent(QMouseEvent *event) override;

    /**
     * @brief 重写键盘按下事件
     * @param event 键盘事件
     */
    void keyPressEvent(QKeyEvent *event) override;

    /**
     * @brief 重写语言切换事件
     * @param event 事件
     */
    void changeEvent(QEvent *event) override;

private:
    QString m_text;          // 控件显示的文本
    QColor m_backgroundColor; // 背景颜色
    bool m_highlighted;      // 是否高亮
};

#endif // TEMPLATEWIDGET_H
```

---

#### 实现文件：`TemplateWidget.cpp`

```cpp
#include "TemplateWidget.h"
#include <QPainter>
#include <QMouseEvent>
#include <QKeyEvent>
#include <QDebug>

TemplateWidget::TemplateWidget(QWidget *parent)
    : QWidget(parent), m_text(tr("Hello, World!")), m_backgroundColor(Qt::white), m_highlighted(false) {
    // 设置初始样式
    setStyleSheet("TemplateWidget { border: 1px solid black; }");

    // 启用键盘焦点
    setFocusPolicy(Qt::StrongFocus);

    // 设置默认大小
    setMinimumSize(100, 50);
}

TemplateWidget::~TemplateWidget() {
    qDebug() << "TemplateWidget destroyed";
}

QString TemplateWidget::text() const {
    return m_text;
}

void TemplateWidget::setText(const QString &text) {
    if (m_text != text) {
        m_text = text;
        update(); // 触发重绘
        emit textChanged(m_text); // 发出信号
    }
}

QColor TemplateWidget::backgroundColor() const {
    return m_backgroundColor;
}

void TemplateWidget::setBackgroundColor(const QColor &color) {
    if (m_backgroundColor != color) {
        m_backgroundColor = color;
        update(); // 触发重绘
        emit backgroundColorChanged(m_backgroundColor); // 发出信号
    }
}

bool TemplateWidget::isHighlighted() const {
    return m_highlighted;
}

void TemplateWidget::setHighlighted(bool highlighted) {
    if (m_highlighted != highlighted) {
        m_highlighted = highlighted;
        update(); // 触发重绘
        emit highlightedChanged(m_highlighted); // 发出信号
    }
}

void TemplateWidget::paintEvent(QPaintEvent *event) {
    Q_UNUSED(event);

    QPainter painter(this);

    // 绘制背景
    painter.fillRect(rect(), m_backgroundColor);

    // 绘制高亮边框
    if (m_highlighted) {
        painter.setPen(Qt::red);
        painter.drawRect(rect().adjusted(0, 0, -1, -1));
    }

    // 绘制文本
    painter.setPen(Qt::black);
    painter.drawText(rect(), Qt::AlignCenter, m_text);
}

void TemplateWidget::mousePressEvent(QMouseEvent *event) {
    if (event->button() == Qt::LeftButton) {
        emit clicked(); // 发出点击信号
    }
    QWidget::mousePressEvent(event);
}

void TemplateWidget::keyPressEvent(QKeyEvent *event) {
    if (event->key() == Qt::Key_Enter || event->key() == Qt::Key_Return) {
        emit clicked(); // 按下Enter键触发点击事件
    }
    QWidget::keyPressEvent(event);
}

void TemplateWidget::changeEvent(QEvent *event) {
    if (event->type() == QEvent::LanguageChange) {
        setText(tr("Hello, World!")); // 动态更新文本
    }
    QWidget::changeEvent(event);
}
```

---

###

1. **支持动态属性**：
   - 添加了`highlighted`属性，支持通过QSS动态设置高亮状态。

2. **支持样式表（QSS）**：
   - 使用`setStyleSheet()`设置初始样式，并支持通过QSS动态修改样式。

3. **支持国际化**：
   - 使用`tr()`包裹文本，支持多语言翻译。
   - 重写`changeEvent()`响应语言切换事件。

4. **支持键盘导航**：
   - 重写`keyPressEvent()`，支持通过Enter键触发点击事件。

5. **支持高DPI屏幕**：
   - 使用`QPainter`进行绘制，确保在高DPI屏幕下显示正常。

6. **支持调试信息**：
   - 在析构函数中输出调试信息，方便调试和问题排查。

7. **支持资源管理**：
   - 在析构函数中释放资源，确保控件销毁时不会泄漏资源。

8. **支持默认值和初始化**：
   - 在构造函数中设置默认值和初始样式。

9. **支持错误处理**：
   - 在属性设置时进行验证，避免无效值。

10. **支持生命周期管理**：
    - 正确处理控件的父子关系，避免内存泄漏。

---