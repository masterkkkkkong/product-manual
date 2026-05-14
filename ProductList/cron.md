# cron — 定时任务调度工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs（Unix 创始团队） |
| 首次发布 | 1975 年（Unix V7） |
| 用途 | 按照约定的时间规则自动执行脚本或命令，实现自动化运维 |
| macOS 自带 | ✅ 是（`/usr/sbin/cron`，但默认服务可能未启用） |

## 🎯 用来做什么？

- **定时执行脚本**：每天凌晨备份数据库、每周清理日志文件、每小时同步数据
- **自动化巡检**：定时检查服务器磁盘空间、服务状态，异常时发送告警
- **定时通知**：每天早上 9 点发送日报邮件、定时推送消息到钉钉/企业微信
- **周期性任务**：每隔一段时间抓取网页、更新缓存、生成报表

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `crontab -l` | 查看当前用户的定时任务列表 |
| `crontab -e` | 编辑当前用户的定时任务（默认编辑器为 vi 或环境变量 `EDITOR` 指定） |
| `crontab -r` | 删除当前用户的所有定时任务（慎用！） |
| `crontab -u username -l` | 查看指定用户的定时任务（需要 root 权限） |
| `EDITOR=nano crontab -e` | 用 nano 编辑器编辑 crontab（macOS 推荐） |
| `crontab <file>` | 从文件导入定时任务（覆盖已有任务） |
| `/usr/sbin/cron` | 启动 cron 守护进程（macOS 需要手动启动） |

## 💡 日常使用示例

### 示例 1：理解 cron 时间表达式

cron 的时间格式由五位组成，用空格分隔：

```
* * * * * command_to_execute
│ │ │ │ │
│ │ │ │ └── 星期 (0-7, 0 和 7 都表示周日)
│ │ │ └──── 月份 (1-12)
│ │ └────── 日期 (1-31)
│ └──────── 小时 (0-23)
└────────── 分钟 (0-59)
```

常用时间示例：

| 表达式 | 含义 |
|--------|------|
| `0 9 * * *` | 每天早上 9:00 |
| `*/5 * * * *` | 每 5 分钟执行一次 |
| `0 0 * * *` | 每天午夜 0:00 |
| `0 9 * * 1-5` | 每个工作日（周一到周五）早上 9:00 |
| `0 2 * * 0` | 每周日凌晨 2:00 |
| `0 0 1 * *` | 每月第一天凌晨 0:00 |
| `30 8 15 * *` | 每月 15 号早上 8:30 |
| `0 */2 * * *` | 每 2 小时执行一次 |

### 示例 2：创建一个定时备份脚本

```bash
# 1. 先编辑 crontab
EDITOR=nano crontab -e

# 2. 在 nano 中输入以下内容并保存
# 每天凌晨 2:00 备份项目数据库到 ~/backups/
0 2 * * * cd /Users/mk/project && pg_dump mydb > ~/backups/db_$(date +\%Y\%m\%d).sql 2>> ~/backups/backup.log

# 注意：crontab 中的 % 需要转义为 \%
# 建议用脚本代替长命令：

# 3. 更好的方式——编写备份脚本
cat > ~/backup.sh << 'SCRIPT'
#!/bin/bash
BACKUP_DIR="$HOME/backups"
DB_NAME="mydb"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
cd "$HOME/project"

# 执行备份
pg_dump "$DB_NAME" > "$BACKUP_DIR/db_$TIMESTAMP.sql"

# 保留最近 30 天的备份，删除旧的
find "$BACKUP_DIR" -name "db_*.sql" -mtime +30 -delete

echo "[$(date)] 备份完成: db_$TIMESTAMP.sql" >> "$BACKUP_DIR/backup.log"
SCRIPT

# 4. 给脚本执行权限
chmod +x ~/backup.sh

# 5. 然后在 crontab 中写
0 2 * * * /Users/mk/backup.sh
```

### 示例 3：监控磁盘空间并发送告警

```bash
# 1. 创建监控脚本
cat > ~/disk_check.sh << 'SCRIPT'
#!/bin/bash
THRESHOLD=90
EMAIL="your@email.com"

# 检查磁盘使用率
USAGE=$(df -h / | tail -1 | awk '{print $5}' | sed 's/%//')

if [ "$USAGE" -gt "$THRESHOLD" ]; then
    echo "⚠️ 磁盘使用率已达 ${USAGE}%，请及时清理！" | \
    mail -s "[告警] 服务器磁盘空间不足" "$EMAIL"
fi
SCRIPT

chmod +x ~/disk_check.sh

# 2. 在 crontab 中添加（每 30 分钟检查一次）
*/30 * * * * /Users/mk/disk_check.sh >> /tmp/disk_check.log 2>&1

# 3. 查看日志确认任务执行
tail -5 /tmp/disk_check.log

# 4. 查看当前所有定时任务
crontab -l
# 输出示例：
# 0 2 * * * /Users/mk/backup.sh
# */30 * * * * /Users/mk/disk_check.sh
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **cron 不执行任务** | 任务没任何反应 | 确认 cron 守护进程在运行：`sudo /usr/sbin/cron`（macOS）；Linux 上用 `systemctl status crond` |
| **脚本手动跑可以，cron 跑就失败** | 脚本在 cron 下行为异常 | cron 环境变量极少，PATH 可能只有 `/usr/bin:/bin`。在脚本开头设置完整 PATH：`export PATH="/usr/local/bin:/usr/bin:/bin"` |
| **macOS 上 cron 需要在隐私授权** | macOS 阻止 cron 访问磁盘/文件 | macOS 10.15+ 需要给 cron 全磁盘访问权限：系统设置 → 隐私与安全性 → 完全磁盘访问权限 → 添加 `/usr/sbin/cron` |
| **% 号被解释** | `date +%Y%m%d` 报错 | 在 crontab 中 `%` 必须转义为 `\%`，或者把命令写成脚本文件 |
| **日志找不到** | 不知道任务有没有执行 | 在命令末尾添加 `>> /tmp/cron.log 2>&1` 重定向输出；或查看系统日志：macOS 用 `log show --predicate 'process == "cron"'` |
| **crontab -e 用不习惯** | vi 编辑器不会用 | 指定用 nano：`EDITOR=nano crontab -e` |
| **任务执行了两遍** | 同一个任务同时被跑两次 | 检查是否同时有 root 和普通用户的 crontab 在跑同一任务 |
| **时区不对** | 任务没按预期时间执行 | cron 使用系统时区；用 `date` 检查当前时区，`TZ=Asia/Shanghai` 可临时指定 |

### macOS 特别说明

macOS 从 Catalina（10.15）开始，更推荐使用 **launchd** 替代 cron 管理定时任务。但 cron 仍然可用，需要注意：

1. **启动 cron 守护进程**：macOS 默认不启动 cron，需要手动启动：
   ```bash
   sudo /usr/sbin/cron
   ```

2. **全磁盘访问权限**：如果脚本需要访问~`桌面`、`下载` 等目录，需要在 系统设置 → 隐私与安全性 → 完全磁盘访问权限 中添加 `/usr/sbin/cron`

3. **日志查看**：macOS 用以下命令查看 cron 日志：
   ```bash
   log show --predicate 'process == "cron"' --last 1h
   ```

## 🔗 参考链接

- [crontab.guru — cron 表达式可视化工具](https://crontab.guru/)
- [Arch Linux Wiki — cron](https://wiki.archlinux.org/title/Cron)
- [macOS 关于 cron 的说明（Apple 官方）](https://support.apple.com/zh-cn/guide/mac-help/mchl31d1a12c/mac)
- [launchd — macOS 推荐的定时任务方案](https://launchd.info/)
