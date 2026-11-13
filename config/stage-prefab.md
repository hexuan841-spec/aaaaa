> 历史配置知识，未核对现行项目代码或配置表；涉及版本兼容、类型和字段时必须与当前项目负责人及实际实现核实。

# ugc_stage_prefab.xlsx 配置知识库



## 一、用途与适用范围

`ugc_stage_prefab.xlsx` 用于配置所有带逻辑的关卡脚本类型。仅在添加新的 UGC 节点时维护该表。

新增节点时依次确认：

1. 是否需要出现在左侧菜单。
2. 场景外观使用哪个预设。
3. 哪些反射路径允许编辑。
4. 默认 JSON 数据结构是什么。
5. 是否属于 `UsedFieldsURL` 包含 `param_base` 的数据节点。

## 二、基础信息字段

### ID

- 填写数字类型的唯一 ID。
- 只要求在当前表内唯一。
- 已删除且当前表内不存在的旧 ID 可以复用。

### Desc 与 Name

- `Desc` 填写描述文本在 `text-ugc.xlsx` 中的文字表 ID。
- `Name` 填写节点名称或展示名称在 `text-ugc.xlsx` 中的文字表 ID。
- 不直接填写中文展示文本。

### Prefab

- 填写节点放置到场景中时使用的预设名称。
- 必须与关卡实际使用的预设名称完全一致，不能填写中文展示名或文字表 ID。
- 需要查找预设名称时，查看 `animAsset_defaulT.xlsx`。

## 三、显示与位置字段

### ShowRange

- `1`：节点本体在运行中显示。
- `0` 或留空：节点本体在运行中不显示。
- 该字段不控制触发范围圈；触发范围圈在运行中始终不显示。

### EditorUI

- 字段已弃用。
- 新增配置时沿用已有同类节点的配置，不做额外扩展。

### SummonPosInfo

- 留空，不需要填写。

### PosInfo

- 固定填写 `/trigger@0/position`。

## 四、路径与默认数据

`UsedFieldsURL` 和 `TemplateData` 都需要启动客户端，通过 GM 中的 UGC 辅助配置生成。两者选择的路径必须保持一致。

### UsedFieldsURL

用途：声明节点中允许编辑的客户端反射路径。

生成步骤：

1. 启动客户端并打开 GM。
2. 点击“UGC辅助配置”。
3. 点击编辑按钮。
4. 选择需要编辑的组件名称并点击对应路径。
5. 在 Editor Console 中找到红色 error 输出。
6. 将 error 中生成的字段内容完整复制到 `UsedFieldsURL`。

### TemplateData

用途：填写客户端解析使用的默认 JSON。JSON 需要覆盖 `UsedFieldsURL` 涉及的部分，并包含这些路径所需的详细默认字段。

生成步骤：

1. 启动客户端并打开 GM。
2. 点击“UGC辅助配置”。
3. 点击编辑按钮。
4. 选择需要修改的路径，选择项必须与 `UsedFieldsURL` 保持一致。
5. 点击“创建”。
6. 不做其他修改，点击关卡脚本返回第一页。
7. 点击“打印”。
8. 在 Editor Console 中找到红色 error 输出。
9. 将 error 中生成的 JSON 完整复制到 `TemplateData`。

### 一致性要求

- `UsedFieldsURL` 声明可编辑路径。
- `TemplateData` 提供这些路径对应的默认 JSON 数据。
- 两者路径不一致时，可能出现字段无法编辑或客户端解析失败。

## 五、数量、图标与菜单分类

### Count

- 限制该类型节点在同一场景中可以同时存在的数量。
- `0` 表示不限制数量。

### Icon

- 表示图标路径。
- 当前无需填写。

### TopSub

- 控制节点在左侧菜单中的两级分类关系。
- 格式为 `母类型,子类型`，使用英文逗号分隔。
- 示例：`1000,2` 表示母类型 `1000` 下的第 `2` 个子类型。

### TopSubText

- 填写 `TopSub` 两级分类对应的文字表 ID。
- 格式为 `母类型文字表ID,子类型文字表ID`，顺序必须与 `TopSub` 一一对应。

### DefaultBuild

- 填写左侧菜单 Icon 使用的图鉴名称。
- 名称必须与 handbook 图鉴中的名称完全一致。
- 需要新增图标或图鉴项时联系 图鉴相关负责人。

## 六、数据节点字段

以下字段只对数据节点生效。以 `UsedFieldsURL` 是否包含 `param_base` 为判断标准；不包含时，以下字段均填写 `0`。

### InputNum

- 表示可以添加的 Get 类型参数数量。
- 对应 `ugc_operation_field.xlsx` 中 `Type=0` 的参数。

### SetterNum

- 表示可以添加的 Set 类型参数数量。
- 对应 `ugc_operation_field.xlsx` 中 `Type=1` 的参数。

### CanUseArg

- `0`：不可使用自定义参数 arg。
- `1`：可以使用自定义参数。

## 七、新增节点检查清单

提交配置前检查：

1. `ID` 在当前表内是唯一数字。
2. `Name`、`Desc` 的文字表 ID 在 `text-ugc.xlsx` 中有效。
3. `Prefab` 与关卡实际预设名称完全一致。
4. `ShowRange` 按节点本体运行时显示需求填写。
5. `EditorUI` 沿用同类节点，`SummonPosInfo` 留空，`PosInfo` 为 `/trigger@0/position`。
6. `UsedFieldsURL` 和 `TemplateData` 通过客户端生成且路径一致。
7. `Count` 符合场景数量限制要求。
8. `TopSub` 与 `TopSubText` 的母、子分类顺序一致。
9. `DefaultBuild` 与 handbook 图鉴名称一致。
10. 根据是否包含 `param_base` 正确填写 `InputNum`、`SetterNum`、`CanUseArg`。

