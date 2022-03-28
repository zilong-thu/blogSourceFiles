---
title: 如何进行 TypeScript 覆盖率分析
date: 2021-11-02 15:31:00
tags:
  - 前端
  - TypeScript
category: 前端
---

自从用了 TypeScript 之后，爱不释手，已经回不去 JavaScript 时代了。最近在公司各个项目中普及 TypeScript，包括对老代码进行 TypeScript 化改造，比较关心项目 TypeScript 覆盖率的变化情况。

## `type-coverage`

调研后找到两个比较好的包，看起来 `type-coverage` 的使用情况更好一些。

|        Package Name        | Weekly Downloads |          Description      |
|----------------------------|------------------|---------------------------|
| type-coverage              |           21,838 |  *.ts, *.js, *.tsx        |
| typescript-coverage-report |           19,997 |                           |

### type-coverage 的基本使用

### type-coverage 工作原理

## 真实可用的 ts 覆盖率统计算法

真实项目中，我们的代码不光是 `js`、`ts`，而是包括了 `vue` 或者 `tsx` 等。以我们的项目而言，如何支持 Vue 文件分析是个问题。

另外，直接以 `type-coverage` 按代码行级别的覆盖率统计，可能会让老项目在 TS 化改造过程中非常没有信心——每天改一点，何时是个头？

其实，可以定义不同的 TS 覆盖率级别（类似于自动驾驶级别），我自己想出来的是这样的：

+ L1 级 TS 覆盖率 = `.ts` 文件与声明了 `lang="ts"` 的 `.vue` 文件的个数占所有文件的比例。
+ L2 级 TS 覆盖率 = `.ts` 文件与声明了 `lang="ts"` 且组件以 `class` 声明的 `.vue` 文件的个数占所有文件的比例。
+ L3 级 TS 覆盖率 = 基于 `type-coverage` 按行统计得到的 ts 覆盖率。
