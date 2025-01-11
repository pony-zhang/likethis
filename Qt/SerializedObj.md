
---

### **设计模板**

#### **1. 头文件 `TemplateObject.h`**
```cpp
#ifndef TEMPLATEOBJECT_H
#define TEMPLATEOBJECT_H

#include <QVariant>
#include <QVariantMap>
#include <QList>
#include <QMap>
#include <QString>

// 枚举类型
enum class Status {
    Unknown,
    Active,
    Inactive,
    Archived
};

// 模板类 TemplateObject，实现Serializable接口
class TemplateObject : public Serializable{
public:
    // 序列化接口
    QVariant serialize() const;

    // 反序列化接口
    void deserialize(const QVariant &data);
public:
    int id = 0;
    QString description;
    double value = 0.0;
    QList<QString> tags;
    QMap<QString, QVariant> properties;
    Status status = Status::Unknown;
};

#endif // TEMPLATEOBJECT_H
```

---

#### **2. 实现文件 `TemplateObject.cpp`**
```cpp
#include "TemplateObject.h"

// TemplateObject 类的实现
QVariant TemplateObject::serialize() const {
    QVariantMap map;
    map["id"] = id;
    map["description"] = description;
    map["value"] = value;

    // 序列化 QList<QString>
    QVariantList tagsList;
    for (const QString &tag : tags) {
        tagsList.append(tag);
    }
    map["tags"] = tagsList;

    // 序列化 QMap<QString, QVariant>
    QVariantMap propertiesMap;
    for (auto it = properties.begin(); it != properties.end(); ++it) {
        propertiesMap[it.key()] = it.value();
    }
    map["properties"] = propertiesMap;

    // 序列化枚举类型
    map["status"] = static_cast<int>(status);

    return map;
}

void TemplateObject::deserialize(const QVariant &data) {
    QVariantMap map = data.toMap();
    id = map["id"].toInt();
    description = map["description"].toString();
    value = map["value"].toDouble();

    // 反序列化 QList<QString>
    QVariantList tagsList = map["tags"].toList();
    tags.clear();
    for (const QVariant &tag : tagsList) {
        tags.append(tag.toString());
    }

    // 反序列化 QMap<QString, QVariant>
    QVariantMap propertiesMap = map["properties"].toMap();
    properties.clear();
    for (auto it = propertiesMap.begin(); it != propertiesMap.end(); ++it) {
        properties[it.key()] = it.value();
    }

    // 反序列化枚举类型
    status = static_cast<Status>(map["status"].toInt());
}
```

---

### **3. 总结**
该设计模板通过 `QVariant` 和 `QVariantMap` 的多重嵌套，实现了复杂数据结构的序列化和反序列化。它支持以下特性：
- 基本类型（如 `int`、`QString`、`double`）。
- 容器类型（如 `QList<QString>`、`QMap<QString, QVariant>`）。
- 枚举类型（如 `Status`）。

这种设计灵活且易于扩展，适合处理包含多种类型数据的复杂类。