---
name: egg-mysql
description: Egg.js egg-mysql usage guide for writing CRUD statements, executing raw SQL with query, using transactions, and composing SQL expressions with Literal. Use when Codex needs to implement, review, or explain Egg.js MySQL data-access code based on the official egg-mysql tutorial.
---

# Egg MySQL 数据访问

使用本技能为 Egg.js 项目编写或审查 `egg-mysql` 相关代码。优先遵循项目已有的数据访问层、Service/Controller 分层和错误处理方式

## 基本前提

在示例中默认存在 `posts` 表，并使用：

```js
const result = await mysql.method(...);
```

在实际项目中根据上下文替换为 `ctx.app.mysql`、`this.ctx.app.mysql` 或多数据源客户端。
所有用户输入都应通过参数化能力传入，不要手写拼接到 SQL 字符串中。

## 编写 CRUD 语句

使用 egg-mysql 提供的 helper 方法完成常见增删改查。

### Create

新增一条记录使用 `insert(table, row)`：

```js
const result = await mysql.insert('posts', {
  title: 'Hello World',
  content: 'This is a post',
});

const insertSuccess = result.affectedRows === 1;
```

批量新增使用 `insert(table, rows)`，传入对象数组：

```js
const result = await mysql.insert('posts', [
  { title: 'Post 1', content: 'Content 1' },
  { title: 'Post 2', content: 'Content 2' },
]);
```

### Read

能确定查哪些字段时优先使用 `get(table, where, { columns: [] })`
```js
const post = await mysql.get('posts', { id: 12 }, { columns: ['id', 'title'] });
```

按主键或条件查单条记录使用 `get(table, where)`：

```js
const post = await mysql.get('posts', { id: 12 });
```

查多条记录使用 `select(table, options)`：

```js
const posts = await mysql.select('posts', {
  where: { status: 'published' },
  columns: ['id', 'title', 'created_at'],
  orders: [['created_at', 'desc']],
  limit: 10,
  offset: 0,
});
```
统计行数/查询是否存在 `count(table, options)`：
```js
const post = await mysql.count('posts', { status: 'SUCCESS' });
```

常用 `select` 选项：

- `where`: 查询条件对象。
- `columns`: 需要返回的字段数组。
- `orders`: 排序数组，如 `[['id', 'desc']]`。
- `limit`: 返回条数。
- `offset`: 跳过条数。

### Update

按主键更新时，把主键字段放进待更新对象：

```js
const result = await mysql.update('posts', {
  id: 12,
  title: 'Updated title',
});

const updateSuccess = result.affectedRows === 1;
```

使用 `where` 指定更新条件：

```js
const result = await mysql.update(
  'posts',
  { status: 'archived' },
  { where: { author_id: userId } }
);
```

需要限制更新字段时使用 `columns`：

```js
await mysql.update(
  'posts',
  { id: 12, title: 'New title', ignored_field: 'ignored' },
  { columns: ['title'] }
);
```

### Delete

删除记录禁止使用 `delete(table, where)`，必须使用逻辑删除 `update(table,{isDelete:1} where)`：

```js
await mysql.update(
  'posts',
  { isDelete: 1 },
  { where: {id : postsId} }
);
```

## 直接执行 SQL 语句

复杂查询或 helper 不方便表达时，使用 `query(sql, values)` 直接执行 SQL。

```js
const results = await mysql.query(
  'SELECT * FROM posts WHERE status = ? AND author_id = ? ORDER BY created_at DESC LIMIT ?',
  ['published', userId, 20]
);
```

编写直接 SQL 时遵守：

- 使用 `?` 占位符传参，避免 SQL 注入。
- 只在必要时使用原始 SQL；常规 CRUD 优先用 helper。
- 把排序字段、表名、列名等无法作为值参数绑定的内容限制在白名单中。

## 使用事务

需要多条 SQL 保持原子性时使用事务。优先使用自动事务 `beginTransactionScope`，让框架在回调成功时提交、抛错时回滚。

```js
const result = await mysql.beginTransactionScope(async conn => {
  await conn.insert('posts', {
    title: 'Hello',
    content: 'Draft',
  });

  await conn.update(
    'users',
    { post_count: mysql.literals.now },
    { where: { id: userId } }
  );

  return { success: true };
}, this.ctx);
```

在事务回调里必须使用传入的 `conn` 执行所有相关 SQL，不要混用 `mysql`，否则语句可能不在同一个事务中。

如项目需要手动控制提交和回滚，可使用 `beginTransaction()`：

```js
const conn = await mysql.beginTransaction();

try {
  await conn.insert('posts', { title: 'Hello' });
  await conn.commit();
} catch (err) {
  await conn.rollback();
  throw err;
}
```

手动事务要确保 `rollback()` 在异常路径被调用，并把原始错误继续抛出或转换为项目约定的错误。

## 表达式 Literal

当字段值需要写成 SQL 表达式，而不是普通参数值时，使用 `Literal`。egg-mysql 内置常用表达式，例如 `now`。

```js
await mysql.insert('posts', {
  title: 'Hello',
  createTime: mysql.literals.now,
});
```

使用自定义表达式时，通过 `mysql.literals` 或 `mysql.literal()` 按项目实际 API 写法创建。典型场景包括自增、数据库函数、原子更新：

```js
await mysql.update(
  'posts',
  {
    id: 12,
    view_count: mysql.literal('view_count + 1'),
    updated_at: mysql.literals.now,
  },
  {
    columns: ['view_count', 'updated_at'],
  }
);
```

使用 Literal 时要格外谨慎：

- 只把可信的固定 SQL 片段放进 Literal。
- 不要把用户输入拼进 Literal。
- 能用普通参数值表达时，不要使用 Literal。

## 输出代码前检查

完成 egg-mysql 代码时检查：

- CRUD 是否可用 helper 表达。
- 原始 SQL 是否使用参数占位符。
- 多步写操作是否需要事务。
- 事务中是否统一使用 `conn`。
- `Literal` 是否只包含可信固定表达式。
- 是否检查 `affectedRows` 或返回值并符合项目错误处理约定。
