# [test markdown style](https://github.com/sunyuan686/blog/issues/13)

好的，这是对您提供的苹果健康（Apple Health）数据导出文件中 `export.xml` 文件结构定义（DTD）的分析和可视化说明文档。

这份文档将帮助您理解每个数据元素的含义、它包含的属性以及它们之间的关系。

------



# Apple 健康 (Health) 数据导出文件 (XML) 结构解析



本文档旨在详细解释从 Apple 健康 App 导出的 `export.xml` 文件的数据结构。该文件的结构由一个 DTD (Document Type Definition) 定义，本解析将以更易于理解的方式，对 DTD 中的各个元素和属性进行说明。



## 概览



导出的 XML 文件以一个根元素 `<HealthData>` 开始，其中包含了导出的元数据（如导出时间和地区设置）、您的个人信息、以及一系列不同类型的数据记录。

主要的记录类型包括：

- **Record**: 通用的、单个时间点或时间段的数据记录（如步数、心率、体重）。
- **Correlation**: 由多个 `Record` 组成的关联数据（如血压，包含收缩压和舒张压）。
- **Workout**: 体能训练的详细记录。
- **ActivitySummary**: 每日健身记录圆环（活动、锻炼、站立）的摘要。
- **ClinicalRecord**: 来自医疗机构的临床记录（如过敏、免疫接种）。
- **Audiogram**: 听力测试图。
- **VisionPrescription**: 视力处方。

------



## 顶级元素





### 1. `<HealthData>` (根元素)



这是整个 XML 文档的根容器，包含了所有健康数据。

| 属性     | 说明                                                         | 类型/要求     |
| -------- | ------------------------------------------------------------ | ------------- |
| `locale` | 导出数据时使用的地区和语言代码。例如 `en_CN` 表示地区为中国，语言为英文。 | `CDATA`, 必需 |

**子元素**:

- `<ExportDate>`
- `<Me>`
- 以及零个或多个 `<Record>`, `<Correlation>`, `<Workout>`, `<ActivitySummary>`, `<ClinicalRecord>`, `<Audiogram>`, `<VisionPrescription>`。



### 2. `<ExportDate>` (导出日期)



记录了这份数据是什么时候从“健康” App 中导出的。

| 属性    | 说明                   | 类型/要求     |
| ------- | ---------------------- | ------------- |
| `value` | 导出的确切日期和时间。 | `CDATA`, 必需 |



### 3. `<Me>` (个人信息)



存储用户的基本健康特征信息，这些信息通常不会频繁改变。

| 属性                                                        | 说明                                       | 类型/要求     |
| ----------------------------------------------------------- | ------------------------------------------ | ------------- |
| `HKCharacteristicTypeIdentifierDateOfBirth`                 | 出生日期                                   | `CDATA`, 必需 |
| `HKCharacteristicTypeIdentifierBiologicalSex`               | 生理性别                                   | `CDATA`, 必需 |
| `HKCharacteristicTypeIdentifierBloodType`                   | 血型                                       | `CDATA`, 必需 |
| `HKCharacteristicTypeIdentifierFitzpatrickSkinType`         | 菲茨帕特里克肤质类型（用于心率传感器算法） | `CDATA`, 必需 |
| `HKCharacteristicTypeIdentifierCardioFitnessMedicationsUse` | 是否使用可能影响心肺健康的药物             | `CDATA`, 必需 |

------



## 主要数据记录类型





### 4. `<Record>` (通用数据记录)



这是最基础也是最常见的数据单元，用于记录绝大多数健康指标。每个 `<Record>` 代表一个在特定时间点或时间段内测量的数据。

**示例**: 一次心率测量、一段时间内的步数总和、体重记录等。

| 属性            | 说明                                                         | 类型/要求     |
| --------------- | ------------------------------------------------------------ | ------------- |
| `type`          | 记录的数据类型标识符。例如 `HKQuantityTypeIdentifierStepCount` (步数)。 | `CDATA`, 必需 |
| `unit`          | 数据的单位。例如 `count` (次), `bpm` (次/分钟), `kg` (千克)。 | `CDATA`, 可选 |
| `value`         | 记录的数值。                                                 | `CDATA`, 可选 |
| `sourceName`    | 数据的来源 App 或设备名称（如：iPhone, Apple Watch）。       | `CDATA`, 必需 |
| `sourceVersion` | 来源 App 或设备的版本号。                                    | `CDATA`, 可选 |
| `device`        | 记录数据的具体硬件设备信息。                                 | `CDATA`, 可选 |
| `creationDate`  | 这条记录在数据库中被创建的时间。                             | `CDATA`, 可选 |
| `startDate`     | 记录的开始时间。                                             | `CDATA`, 必需 |
| `endDate`       | 记录的结束时间。对于瞬时数据，`startDate` 和 `endDate` 相同。 | `CDATA`, 必需 |

**子元素**:

- `<MetadataEntry>`: 键值对形式的元数据，提供关于此记录的附加信息。
- `<HeartRateVariabilityMetadataList>`: 针对心率变异性（HRV）记录，包含一系列瞬时心跳读数。



### 5. `<Correlation>` (关联数据)



用于将多个独立的 `<Record>` 元素组合成一个有意义的复合记录。

**最常见的示例**: 血压记录，它会包含一个收缩压 `<Record>` 和一个舒张压 `<Record>`。

| 属性            | 说明                                                         | 类型/要求     |
| --------------- | ------------------------------------------------------------ | ------------- |
| `type`          | 关联数据的类型标识符。例如 `HKCorrelationTypeIdentifierBloodPressure`。 | `CDATA`, 必需 |
| `sourceName`    | 数据来源 App 或设备。                                        | `CDATA`, 必需 |
| `sourceVersion` | 来源版本。                                                   | `CDATA`, 可选 |
| `device`        | 记录数据的具体硬件设备信息。                                 | `CDATA`, 可选 |
| `creationDate`  | 记录在数据库中被创建的时间。                                 | `CDATA`, 可选 |
| `startDate`     | 关联记录的开始时间。                                         | `CDATA`, 必需 |
| `endDate`       | 关联记录的结束时间。                                         | `CDATA`, 必需 |

**子元素**:

- `<MetadataEntry>`: 附加的元数据。
- `<Record>`: 构成此关联数据的子记录。**注意**：这些子记录也会在文件的顶层作为独立的 `<Record>` 出现。



### 6. `<Workout>` (体能训练)



详细记录一次体能训练活动，如跑步、游泳或骑行。

| 属性                                                         | 说明                                                         | 类型/要求     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------- |
| `workoutActivityType`                                        | 体能训练的类型。例如 `HKWorkoutActivityTypeRunning` (跑步)。 | `CDATA`, 必需 |
| `duration`                                                   | 训练持续的总时长。                                           | `CDATA`, 可选 |
| `durationUnit`                                               | 时长的单位（如 `min`）。                                     | `CDATA`, 可选 |
| `totalDistance`                                              | 训练的总距离。                                               | `CDATA`, 可选 |
| `totalDistanceUnit`                                          | 距离的单位（如 `km`）。                                      | `CDATA`, 可选 |
| `totalEnergyBurned`                                          | 训练消耗的总能量（大卡）。                                   | `CDATA`, 可选 |
| `totalEnergyBurnedUnit`                                      | 能量的单位（如 `kcal`）。                                    | `CDATA`, 可选 |
| `sourceName`, `sourceVersion`, `device`, `creationDate`, `startDate`, `endDate` | 与 `<Record>` 中的属性含义相同。                             | -             |

**子元素**:

- `<MetadataEntry>`: 元数据。
- `<WorkoutEvent>`: 训练过程中的事件，如“暂停”或“继续”。
- `<WorkoutRoute>`: 训练的 GPS 路线数据。它内部的 `<FileReference>` 指向一个单独的 `.gpx` 路线文件。
- `<WorkoutStatistics>`: 训练期间各种指标的统计数据（如平均心率、最大心率等）。



### 7. `<ActivitySummary>` (健身记录摘要)



记录每天的“健身圆环”完成情况。每天会有一条这样的记录。

| 属性                     | 说明                             | 类型/要求     |
| ------------------------ | -------------------------------- | ------------- |
| `dateComponents`         | 记录的日期（年-月-日）。         | `CDATA`, 可选 |
| `activeEnergyBurned`     | 当天消耗的动态能量（活动圆环）。 | `CDATA`, 可选 |
| `activeEnergyBurnedGoal` | 当天动态能量的目标值。           | `CDATA`, 可选 |
| `activeEnergyBurnedUnit` | 能量单位（通常是 `kcal`）。      | `CDATA`, 可选 |
| `appleMoveTime`          | 当天的活动分钟数。               | `CDATA`, 可选 |
| `appleMoveTimeGoal`      | 当天活动分钟数的目标。           | `CDATA`, 可选 |
| `appleExerciseTime`      | 当天的锻炼分钟数（锻炼圆环）。   | `CDATA`, 可选 |
| `appleExerciseTimeGoal`  | 当天锻炼分钟数的目标。           | `CDATA`, 可选 |
| `appleStandHours`        | 当天的站立小时数（站立圆环）。   | `CDATA`, 可选 |
| `appleStandHoursGoal`    | 当天站立小时数的目标。           | `CDATA`, 可选 |



### 8. `<ClinicalRecord>` (临床记录)



存储从兼容的医疗机构或健康 App 同步的医疗记录。

| 属性               | 说明                                                         | 类型/要求     |
| ------------------ | ------------------------------------------------------------ | ------------- |
| `type`             | 临床记录的类型（如：过敏、用药、免疫接种）。                 | `CDATA`, 必需 |
| `identifier`       | 记录的唯一标识符。                                           | `CDATA`, 必需 |
| `sourceName`       | 来源机构或 App 的名称。                                      | `CDATA`, 必需 |
| `sourceURL`        | 来源机构的网址。                                             | `CDATA`, 必需 |
| `fhirVersion`      | 记录所遵循的 FHIR (Fast Healthcare Interoperability Resources) 标准版本。 | `CDATA`, 必需 |
| `receivedDate`     | 收到此记录的日期。                                           | `CDATA`, 必需 |
| `resourceFilePath` | 指向包含详细医疗数据的 JSON 文件的路径。                     | `CDATA`, 必需 |



### 9. `<Audiogram>` (听力图)



存储听力测试的结果。

| 属性                                                         | 说明                             | 类型/要求 |
| ------------------------------------------------------------ | -------------------------------- | --------- |
| `type`, `sourceName`, `sourceVersion`, `device`, `creationDate`, `startDate`, `endDate` | 与 `<Record>` 中的属性含义类似。 | -         |

**子元素**:

- `<MetadataEntry>`: 元数据。
- `<SensitivityPoint>`: 核心数据点，记录在特定频率下，左耳或右耳能听到的最小声音阈值。包含频率（`frequencyValue`）、听力阈值（`leftEarValue`/`rightEarValue`）等详细属性。



### 10. `<VisionPrescription>` (视力处方)



存储眼镜或隐形眼镜的处方信息。

| 属性             | 说明                           | 类型/要求     |
| ---------------- | ------------------------------ | ------------- |
| `type`           | 处方类型（如眼镜、隐形眼镜）。 | `CDATA`, 必需 |
| `dateIssued`     | 处方开具日期。                 | `CDATA`, 必需 |
| `expirationDate` | 处方过期日期。                 | `CDATA`, 可选 |
| `brand`          | 镜片品牌。                     | `CDATA`, 可选 |

**子元素**:

- `<RightEye>`: 右眼处方详情，包含球镜（`sphere`）、柱镜（`cylinder`）、轴位（`axis`）、瞳距（`farPD`）等。
- `<LeftEye>`: 左眼处方详情，属性与 `<RightEye>` 相同。
- `<Attachment>`: 指向处方扫描件或照片的附件。
- `<MetadataEntry>`: 元数据。

------

希望这份解析文档能帮助您更好地理解和使用您的 Apple 健康数据！