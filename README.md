# 场站断连回传的范围对账与补齐入湖

这是本地演练用的合成来源资料。CSV 使用 UTF-8、有首行字段名，空字段表示未提供。除非字段语义明确为零，空值没有数值含义。

## 资料契约

region_rows 与 region_manifests 的时间为 UTC，边界左闭右开，格宽十分钟。区域清单 supersedes 仅作用于声明范围，complete=false 表示没有覆盖承诺。离线设备用整数秒，校时模型 time_utc = anchor + offset 秒 + (tick - origin_tick) 秒，同版模型只应用 tick_from 到 tick_to 内的片段边界。包给出每个不可拆间隔的增量，片序范围含首尾。空片可明确没有增加量，不能视为时钟证据。点位映射的有效区间按校时后整格判断。

以下文件位于 `data/`。

- `point_mappings.csv`（2 条）：`recorder`、`meter`、`point`、`from`、`to`
- `region_manifests.csv`（3 条）：`wave`、`manifest`、`point`、`from`、`to`、`complete`、`supersedes`
- `region_rows.csv`（6 条）：`wave`、`manifest`、`at`、`kind`、`kwh`
- `edge_manifests.csv`（2 条）：`wave`、`batch`、`recorder`、`meter`、`first_shard`、`last_shard`、`start_tick`、`end_tick`
- `edge_fragments.csv`（6 条）：`wave`、`batch`、`shard`、`lo`、`hi`、`state`、`kwh`
- `clock_revisions.csv`（3 条）：`wave`、`recorder`、`rev`、`tick_from`、`tick_to`、`origin_tick`、`anchor`、`offset`
- `downstream_requests.csv`（3 条）：`wave`、`request`、`point`、`from`、`to`

`manifest.json` 记录完整资料包的列名、行数、校验值和跨表引用。完整资料包可含业务冲突或同一来源的多次投递，校验器保留这些原始记录。

## 本地查看

适用 Python 3.11，无需安装第三方库或构建。

```sh
python3 inspect_inputs.py
python3 -m unittest -v test_input_pack
```

查看命令打印各原始表的记录数。单元测试校验资料结构、引用与文件完整性，可离线执行。
