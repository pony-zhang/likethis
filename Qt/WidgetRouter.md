
---

### **1. 路由和界面的设计**

#### **1.1 `IBasePage` 接口类**

```cpp
#ifndef IBASEPAGE_H
#define IBASEPAGE_H

#include <QWidget>
#include <QVariant>

class IBasePage : public QWidget {
    Q_OBJECT
public:
    explicit IBasePage(QWidget *parent = nullptr) : QWidget(parent) {}
    virtual ~IBasePage() {}

    // 页面初始化
    virtual void initialize() = 0;

    // 进入页面时的钩子函数
    virtual void onEnter(const QVariant &data) {
        Q_UNUSED(data);
        qDebug() << "Entering page:" << metaObject()->className();
    }

    // 退出页面时的钩子函数
    virtual void onExit() {
        qDebug() << "Exiting page:" << metaObject()->className();
    }

signals:
    // 页面切换请求
    void navigateTo(const QString &route, const QVariant &data = QVariant());

    // 请求外部容器切换窗口
    void requestSwitchWindow(QWidget *widget);
};

#endif // IBASEPAGE_H
```

---

#### **1.2 窗口管理器 (`WindowManager`)**
保持不变，负责管理窗口的创建和销毁。

```cpp
#ifndef WINDOWMANAGER_H
#define WINDOWMANAGER_H

#include <QMap>
#include <QString>
#include <QSharedPointer>
#include "IBasePage.h"

class WindowManager {
public:
    static WindowManager& instance() {
        static WindowManager instance;
        return instance;
    }

    // 注册页面
    void registerPage(const QString &route, std::function<IBasePage*()> creator) {
        pageCreators[route] = creator;
    }

    // 获取页面实例
    QSharedPointer<IBasePage> getPage(const QString &route) {
        if (!pages.contains(route)) {
            if (pageCreators.contains(route)) {
                pages[route] = QSharedPointer<IBasePage>(pageCreators[route]());
                pages[route]->initialize();
            }
        }
        return pages[route];
    }

private:
    WindowManager() {}
    ~WindowManager() {}

    QMap<QString, std::function<IBasePage*()>> pageCreators; // 页面创建函数
    QMap<QString, QSharedPointer<IBasePage>> pages;          // 页面实例
};

#endif // WINDOWMANAGER_H
```

---

#### **1.3 路由管理类 (`Router`)**
通过信号和槽机制通知外部容器切换窗口。

```cpp
#ifndef ROUTER_H
#define ROUTER_H

#include <QObject>
#include <QString>
#include <QVariant>
#include "IBasePage.h"
#include "WindowManager.h"

class Router : public QObject {
    Q_OBJECT
public:
    explicit Router(QObject *parent = nullptr) : QObject(parent) {}

    // 导航到指定路由
    void navigateTo(const QString &route, const QVariant &data = QVariant()) {
        if (currentPage) {
            currentPage->onExit(); // 调用退出钩子
        }

        currentPage = WindowManager::instance().getPage(route);
        if (currentPage) {
            currentPage->onEnter(data); // 调用进入钩子

            // 通知外部容器切换窗口
            emit requestSwitchWindow(currentPage.data());

            // 连接页面切换信号
            connect(currentPage.data(), &IBasePage::navigateTo, this, &Router::navigateTo);
        }
    }

signals:
    // 请求外部容器切换窗口
    void requestSwitchWindow(QWidget *widget);

private:
    QSharedPointer<IBasePage> currentPage;
};

#endif // ROUTER_H
```
---

### **5. 总结**
通过改进设计：
- **添加钩子函数**：`onEnter` 和 `onExit` 增强了页面的生命周期管理。
- **解耦路由和窗口管理**：通过信号和槽机制通知外部容器切换窗口，使 `Router` 更加通用。
- **支持数据传递**：在导航时可以传递数据，增强了灵活性。