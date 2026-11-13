> 历史配置知识，未核对现行项目代码或配置表；涉及版本兼容、类型和字段时必须与当前项目负责人及实际实现核实。

# UGC 配置表知识库



---

## 一、Operation 配置说明

Operation 配置用于定义一个可被 UGC 节点使用的操作。它本质上是服务器开放给客户端和用户配置的一组操作能力。部分字段由服务器维护，部分字段由策划维护。

### 1.1 服务器维护字段

#### OperationName（操作名）

- 服务器和客户端约定的操作函数名。
- 用户界面中不会直接展示 OperationName，而是展示对应的 DisplayName。
- 在最终生成的 JSON 中，仍然会使用 OperationName 作为实际操作标识。

#### OutputType（输入/输出数据类型）

- 用于标识该操作需要的数据类型，主要用于类型校验。
- 当前只允许以下 6 个值，且大小写和拼写必须完全匹配：`Int32`、`Boolean`、`Single`、`String`、`Table`、`Any`。
- `Any` 表示任意类型。
- Type=0（input/获取）时，按照服务器代码的返回值类型填写。
- Type=1（set/设置）时，按照服务器接口接收的参数类型填写。例如服务器接收 C# `float`（System.Single）速度参数，则填写 `Single`。
- 在填写操作面板时，配置的数据类型必须和 OutputType 保持一致。虽然类型不一致时服务器可能尝试转换，但为避免异常或精度问题，不应依赖自动转换。

#### Component（组件）

- 表示该操作在服务器上归属于哪个组件。
- 直接填写组件名字符串，例如 `MovementComponent`，并与代码中的组件名保持一致。
- 只有当某个预设拥有对应组件时，才允许使用该操作。
- 客户端会结合 `prefab_component.xlsx` 表进行合法性筛选，同时也会根据 Component 在选择界面中对操作进行分类展示。

#### Type（操作类型）

- 用于区分当前 Operation 是获取（input）操作还是设置（set）操作。
- `0`：input，例如“获取当前生命值”。
- `1`：set，例如“设置当前生命值”。
- 客户端会根据 Type 在不同的选择界面中筛选可用操作，避免用户在错误场景中选择不适用的 Operation。

### 1.2 策划维护字段

#### ScriptFieldID

- Operation 默认可以直接在编辑框中展示和填写。
- 如果需要接入额外配置能力（如下拉框、共享信号、保留小数等特殊表现），则需要借用 `ugc_levelscript_field.xlsx` 表中的配置。
- 使用方式：在 ScriptFieldID 中填写 `ugc_levelscript_field.xlsx` 对应行的数字 `ID` 列值；不要填写 FieldName。
- **注意**：采用这种方式时，`FieldName` 字段本身不会被实际使用，但仍然需要保证唯一性。建议使用 `Op_操作名` 的命名方式，例如 `Op_SetHealth`。

#### DisplayName（操作文本）

- 用于在客户端界面中展示给用户看的操作名称。
- 应使用用户能理解的描述，例如"获取生命值""设置移动速度""是否处于燃烧状态"等。

---

## 二、LevelScriptField 配置说明

本表用于配置 LevelScript 字段在 UGC 编辑界面中的显示方式、输入方式和校验规则。

> 配置时需要保证字段标识、显示文本和限制条件一致，否则可能导致客户端、配置表和服务器解析结果不一致。

### 2.1 字段标识

#### FieldName

- 字段的唯一标识。客户端、配置表和服务器都会使用同一个 FieldName，三处拼写必须完全一致。
- **重要**：如果字段含义或名称需要调整，不要直接修改旧 FieldName，应新增一行并使用新的 FieldName 与新的、不重复的数字 ID。旧行可以删除，但不要复用其 ID。
- 涉及历史字段删除或兼容性时，必须先联系 项目相关负责人，并在 UGC 群内同步。客户端和服务器修改均完成之前，禁止更新配置表、打表或上传。

### 2.2 信号相关配置

#### SignType

- 用于配置信号字段前方的 icon，主要影响界面展示。
- `0` 或留空：普通字段。
- `1`：发送信号。
- `2`：接收信号。

#### ShareData

- 用于控制字段是否共享信号，或是否引用指定实例。
- **值 = 1**：共享信号。该字段可以编辑，也可以选择其他所有信号。若配置接收信号且需要共享选择能力，应同时填写 `SignType=2` 和 `ShareData=1`：前者控制接收信号图标，后者开启共享信号选择。
- **值 = 2**：多实例列表。ShareDataParam1 中填写 `ugc_stage_prefab.xlsx` 的数字 ID，多个 ID 用英文逗号分隔，例如 `101,102`。这些实例在运行时对应的 batchID 都可以被选择。
- **值 = 3**：单个实例引用。ShareDataParam1 只能填写一个来自 `ugc_stage_prefab.xlsx` 的数字 ID。

#### ShareDataParam1

- 用于补充 ShareData 的信息（见 ShareData 说明）。

#### Desc

- 文字表 ID，对应 `text-ugc.xlsx`。
- 界面说明文本需要在该文字表中维护。

#### Hide

- 控制字段是否显示。
- **值 = 0 或为空**：显示。
- **值 = 1**：隐藏，该字段不会在对应编辑界面中展示。

### 2.3 数据限制字段

| 字段名 | 说明 |
|--------|------|
| MinValue | 只对数字字段生效，表示最小值。为空时默认不限制。 |
| MaxValue | 只对数字字段生效，表示最大值。为空时默认不限制。 |
| KeepDecimals | 只对数字字段生效，表示保留几位小数。为空时默认不保留小数。 |
| MinLength | 只对文本输入字段生效，表示最少字符数。 |
| MaxLength | 只对文本输入字段生效，表示最多字符数。 |

### 2.4 下拉框模式

#### OptionValue

- 用于把字段显示为下拉框。
- 配置格式：`A:文字表ID, B:文字表ID`（文字表 ID 对应 `text-ugc.xlsx`）。
- 用户在界面中看到的是文字表中的文本，但实际写入配置的是 A、B 这类字符串值。

#### VisibleOptionValue

- 用于配合 OptionValue 控制字段显示条件。
- 配置格式：`字段ID:取值/取值`。字段 ID 是 `ugc_levelscript_field.xlsx` 数字 `ID` 列的值，不是 FieldName。
- 示例：`145:BatchNode/Signal` 表示当数字 ID 为 145 的字段取值为 BatchNode 或 Signal 时，当前字段才显示；否则隐藏。多个取值之间是“或”的关系。

### 2.5 输入模式

#### InputStyle

- 用于控制字段在编辑面板中的输入方式。

| 值 | 说明 |
|----|------|
| 1 | 需要配合 OptionValue 使用，提供预设内容的同时允许用户自定义输入（如 tag）。 |
| 2 | 要求该字段是列表，且列表元素的数据类型必须是一个继承自 `trigger_condition_prefab_death_count` 的类。点击加号后弹出左侧预设面板，用户可将预设添加到列表中。若无法确认继承关系，请联系 项目相关负责人。 |
| 3 | 要求该字段是由已定义的自定义非基础类型构成的列表，不要求继承指定基类。点击添加后新增一个自定义子项，左侧面板展开该子项中的每个可编辑字段。 |

---

## 三、配置原则总结

1. **优先保证 FieldName 稳定**：不直接修改已被使用的字段名，改用新增方式。
2. **根据字段用途配置展示方式、输入方式和校验规则**。
3. **涉及文字展示**：维护 `text-ugc.xlsx`。
4. **涉及实例选择**：确认 `ugc_stage_prefab.xlsx` 中的实例 ID 正确。
5. **类型一致性**：Operation 的数据类型配置尽量与 OutputType 严格对应，避免转换异常。
6. **Operation 扩展字段关联**：ScriptFieldID 填 `ugc_levelscript_field.xlsx` 的数字 ID；对应行的 FieldName 建议使用 `Op_操作名` 格式，如 `Op_SetHealth`，降低重复风险。

---

## 四、涉及的关联配置表

| 配置表文件名 | 用途 |
|------------|------|
| `ugc_levelscript_field.xlsx` | LevelScript 字段扩展配置 |
| `text-ugc.xlsx` | 界面文字展示（DisplayName、Desc、OptionValue 文本等） |
| `prefab_component.xlsx` | 预设与组件的对应关系，用于 Operation 合法性筛选 |
| `ugc_stage_prefab.xlsx` | 预设实例 ID，用于 ShareData=2/3 时的实例引用 |

