# PostgreSQL 数据库复制工具

这是一个由AI辅助开发的PostgreSQL数据库复制工具，支持通过SSH隧道连接远程数据库，并提供数据脱敏功能。提供Web界面进行配置和操作。

## 功能特点

- 支持通过SSH隧道连接远程PostgreSQL数据库
- 支持选择性复制表和列
- 提供多种数据脱敏方法：
  - 部分掩码（保留首尾字符）
  - 哈希掩码
  - 随机替换
- Web界面操作，简单直观
- 详细的日志记录
- 使用配置文件管理连接信息和脱敏规则

## 安装

1. 克隆仓库：
```bash
git clone [repository_url]
cd dbcopy
```

2. 安装依赖：
```bash
pip install -r requirements.txt
```

## 启动Web服务

1. 运行Web服务：
```bash
python app.py
```

2. 在浏览器中访问：
```
http://localhost:5000
```

## 配置说明

通过Web界面可以方便地配置以下内容：

1. SSH连接信息：
   - 主机地址
   - 端口
   - 用户名
   - 密钥文件路径

2. 源数据库配置：
   - 主机
   - 端口
   - 数据库名
   - 用户名
   - 密码

3. 目标数据库配置：
   - 主机
   - 端口
   - 数据库名
   - 用户名
   - 密码

4. 表配置：
   - 可以手动添加表
   - 可以从数据库获取表列表
   - 支持配置要复制的列
   - 支持配置数据脱敏规则

配置示例：
```yaml
ssh:
  host: remote.example.com
  port: 22
  username: your_username
  # 使用密钥认证（推荐）
  private_key_path: ~/.ssh/id_rsa
  # 或使用密码认证（不推荐）
  # password: your_password

source_db:
  host: localhost  # 通过SSH隧道访问的数据库主机
  port: 5432
  database: source_db_name
  username: db_user
  password: db_password

target_db:
  host: localhost
  port: 5432
  database: target_db_name
  username: db_user
  password: db_password

# 要复制的表配置
tables:
  - name: users
    # 如果为空则复制所有列
    columns: []
    # 脱敏规则
    mask_rules:
      - column: email
        method: partial  # 可选: partial, hash, random
      - column: phone
        method: partial
  - name: orders
    columns: [id, order_number, created_at, status]
    mask_rules:
      - column: order_number
        method: hash

## 脱敏方法说明

1. `partial`: 保留首尾字符，中间用星号代替
   - 例如：`john@example.com` -> `j***@example.com`

2. `hash`: 使用MD5哈希替换整个值
   - 例如：`12345` -> `827ccb0eea8a706c4c34a16891f84e7b`

3. `random`: 保持原有长度，但用随机字符替换
   - 例如：`secret` -> `x7k9p2`

## 注意事项

1. 请确保SSH密钥或密码配置正确
2. 建议使用SSH密钥认证而不是密码认证
3. 目标数据库需要有足够的权限创建表和写入数据
4. 建议在执行大规模数据复制前先进行小规模测试
5. 所有密码和敏感信息应妥善保管，不要提交到版本控制系统

## 日志

- Web应用日志保存在 `web_app.log` 文件中
- 数据库复制操作日志保存在 `db_copy.log` 文件中 